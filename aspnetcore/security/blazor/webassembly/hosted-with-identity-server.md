---
title: Id サーバーをBlazor使用して、ASP.NET Core webasのホスト型アプリをセキュリティで保護する
author: guardrex
description: ホスティングサーバーバックエンドBlazorを使用する Visual Studio 内から認証を使用し[IdentityServer](https://identityserver.io/)て新しいホスト型アプリを作成するには
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: f8de07e2e21ca19b5c4e95839e7b7e621c335ad0
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110949"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="4a598-103">Id サーバーをBlazor使用して、ASP.NET Core webasのホスト型アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="4a598-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="4a598-104">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4a598-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="4a598-105">この記事のガイダンスは、ASP.NET Core 3.2 Preview 4 に適用されます。</span><span class="sxs-lookup"><span data-stu-id="4a598-105">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="4a598-106">このトピックは、4月24日金曜日の Preview 5 に対応するよう更新されます。</span><span class="sxs-lookup"><span data-stu-id="4a598-106">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="4a598-107">ユーザーと API 呼び出しBlazorを認証するためにユーザーを[使用する](https://identityserver.io/)、Visual Studio で新しいホスト型アプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="4a598-107">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="4a598-108">Visual Studio を使用して、 \*\* Blazor新しいアプリを作成し\*\*ます。</span><span class="sxs-lookup"><span data-stu-id="4a598-108">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="4a598-109">詳細については、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a598-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="4a598-110">[**新しいBlazorアプリの作成**] ダイアログで、[**認証**] セクションの [**変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="4a598-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="4a598-111">**個々のユーザーアカウント**を選択し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="4a598-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="4a598-112">[**詳細設定**] セクションで [ホストされている**ASP.NET Core** ] チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4a598-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="4a598-113">**[作成]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4a598-113">Select the **Create** button.</span></span>

<span data-ttu-id="4a598-114">コマンドシェルでアプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4a598-114">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="4a598-115">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など`-o BlazorSample`) を指定して出力オプションを含めます。</span><span class="sxs-lookup"><span data-stu-id="4a598-115">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="4a598-116">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="4a598-116">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="4a598-117">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="4a598-117">Server app configuration</span></span>

<span data-ttu-id="4a598-118">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="4a598-118">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="4a598-119">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="4a598-119">Startup class</span></span>

<span data-ttu-id="4a598-120">クラス`Startup`には、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="4a598-120">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="4a598-121">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="4a598-121">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="4a598-122">ID:</span><span class="sxs-lookup"><span data-stu-id="4a598-122">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="4a598-123">サーバーには、次<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>のように、既定の ASP.NET Core 規則を設定する追加のヘルパーメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="4a598-123">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="4a598-124">次のように<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>追加のヘルパーメソッドを使用して認証します。これにより、アプリは、サービスによって生成された JWT トークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="4a598-124">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="4a598-125">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="4a598-125">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="4a598-126">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="4a598-126">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="4a598-127">Open ID Connect (OIDC) エンドポイントを公開するサーバーミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="4a598-127">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="4a598-128">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="4a598-128">AddApiAuthorization</span></span>

<span data-ttu-id="4a598-129">ヘルパー <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>メソッドは、ASP.NET Core シナリオ用に[サーバー](https://identityserver.io/)を構成します。</span><span class="sxs-lookup"><span data-stu-id="4a598-129">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="4a598-130">サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="4a598-130">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="4a598-131">一般に、最も一般的なシナリオでは、不要な複雑さが公開されます。</span><span class="sxs-lookup"><span data-stu-id="4a598-131">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="4a598-132">そのため、適切な出発点として考えられる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="4a598-132">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="4a598-133">認証を変更する必要が生じた場合でも、アプリの要件に合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="4a598-133">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="4a598-134">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="4a598-134">AddIdentityServerJwt</span></span>

<span data-ttu-id="4a598-135">ヘルパー <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>メソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="4a598-135">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="4a598-136">このポリシーは、id URL 空間`/Identity`内のサブパスにルーティングされたすべての要求を id が処理できるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="4a598-136">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="4a598-137">は<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 、他のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="4a598-137">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="4a598-138">さらに、このメソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="4a598-138">Additionally, this method:</span></span>

* <span data-ttu-id="4a598-139">既定の`{APPLICATION NAME}API`スコープの`{APPLICATION NAME}API`を使用して、API リソースをに登録します。</span><span class="sxs-lookup"><span data-stu-id="4a598-139">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="4a598-140">アプリに対して、サービスによって発行されたトークンを検証するように JWT ベアラートークンミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="4a598-140">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="4a598-141">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="4a598-141">WeatherForecastController</span></span>

<span data-ttu-id="4a598-142">`WeatherForecastController` (*Controllers/WeatherForecastController*) で、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)属性がクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4a598-142">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="4a598-143">属性は、ユーザーがリソースにアクセスするための既定のポリシーに基づいて承認される必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="4a598-143">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="4a598-144">既定の承認ポリシーは、既定の認証スキームを使用するように構成され<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>ます。これは、前に説明したポリシースキームによって設定されます。</span><span class="sxs-lookup"><span data-stu-id="4a598-144">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="4a598-145">ヘルパーメソッドは、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>アプリに対する要求の既定のハンドラーとしてを構成します。</span><span class="sxs-lookup"><span data-stu-id="4a598-145">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="4a598-146">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="4a598-146">ApplicationDbContext</span></span>

<span data-ttu-id="4a598-147">`ApplicationDbContext` (*Data/applicationdbcontext .cs*) では、id には<xref:Microsoft.EntityFrameworkCore.DbContext> 、ユーザーのスキーマを含むように拡張<xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>された例外と同じが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4a598-147">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="4a598-148"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="4a598-148"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="4a598-149">データベーススキーマを完全に制御するには、使用可能な Id <xref:Microsoft.EntityFrameworkCore.DbContext>クラスの1つを継承し、 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating`メソッドでを呼び出して、id スキーマを含めるようにコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="4a598-149">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="4a598-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="4a598-150">OidcConfigurationController</span></span>

<span data-ttu-id="4a598-151">`OidcConfigurationController` (*Controllers/OidcConfigurationController*) では、oidc パラメーターを提供するためにクライアントエンドポイントがプロビジョニングされます。</span><span class="sxs-lookup"><span data-stu-id="4a598-151">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="4a598-152">アプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="4a598-152">App settings files</span></span>

<span data-ttu-id="4a598-153">プロジェクトルートのアプリ設定ファイル (*appsettings. json*) で、構成され`IdentityServer`ているクライアントの一覧を説明するセクションです。</span><span class="sxs-lookup"><span data-stu-id="4a598-153">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="4a598-154">次の例には、1つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="4a598-154">In the following example, there's a single client.</span></span> <span data-ttu-id="4a598-155">クライアント名はアプリケーション名に対応し、OAuth `ClientId`パラメーターに規約によってマップされます。</span><span class="sxs-lookup"><span data-stu-id="4a598-155">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="4a598-156">プロファイルは、構成されているアプリの種類を示します。</span><span class="sxs-lookup"><span data-stu-id="4a598-156">The profile indicates the app type being configured.</span></span> <span data-ttu-id="4a598-157">プロファイルは、サーバーの構成プロセスを簡略化する規則を推進するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4a598-157">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="4a598-158">開発環境のアプリ設定ファイル (appsettings) で、*appsettings.Development.json*この`IdentityServer`セクションでは、プロジェクトルートでの json の署名に使用されるキーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4a598-158">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="4a598-159">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="4a598-159">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="4a598-160">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="4a598-160">Authentication package</span></span>

<span data-ttu-id="4a598-161">個々のユーザーアカウント (`Individual`) を使用するようにアプリを作成すると、アプリはアプリのプロジェクト`Microsoft.AspNetCore.Components.WebAssembly.Authentication`ファイルでパッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="4a598-161">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="4a598-162">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="4a598-162">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="4a598-163">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="4a598-163">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="4a598-164">前`{VERSION}`のパッケージ参照のを、 <xref:blazor/get-started>この記事に示さ`Microsoft.AspNetCore.Blazor.Templates`れているパッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4a598-164">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="4a598-165">API 承認のサポート</span><span class="sxs-lookup"><span data-stu-id="4a598-165">API authorization support</span></span>

<span data-ttu-id="4a598-166">ユーザー認証のサポートは、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`パッケージ内で提供される拡張メソッドによってサービスコンテナーに接続されます。</span><span class="sxs-lookup"><span data-stu-id="4a598-166">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="4a598-167">このメソッドは、アプリが既存の承認システムと対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="4a598-167">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="4a598-168">既定では、から`_configuration/{client-id}`の規則によって、アプリの構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4a598-168">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="4a598-169">規則により、クライアント ID はアプリのアセンブリ名に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4a598-169">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="4a598-170">この URL は、オプションを指定してオーバーロードを呼び出すことによって、別のエンドポイントを指すように変更できます。</span><span class="sxs-lookup"><span data-stu-id="4a598-170">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="4a598-171">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="4a598-171">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="4a598-172">Index ページ</span><span class="sxs-lookup"><span data-stu-id="4a598-172">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="4a598-173">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="4a598-173">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="4a598-174">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4a598-174">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="4a598-175">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4a598-175">LoginDisplay component</span></span>

<span data-ttu-id="4a598-176">コンポーネント (*shared/Logindisplay*) は`MainLayout` 、コンポーネント (*shared/mainlayout*) にレンダリングされ、次の動作を管理します。 `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="4a598-176">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="4a598-177">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="4a598-177">For authenticated users:</span></span>
  * <span data-ttu-id="4a598-178">現在のユーザー名を表示します。</span><span class="sxs-lookup"><span data-stu-id="4a598-178">Displays the current user name.</span></span>
  * <span data-ttu-id="4a598-179">ASP.NET Core Id のユーザープロファイルページへのリンクを提供します。</span><span class="sxs-lookup"><span data-stu-id="4a598-179">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="4a598-180">アプリからログアウトするためのボタンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="4a598-180">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="4a598-181">匿名ユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="4a598-181">For anonymous users:</span></span>
  * <span data-ttu-id="4a598-182">登録するオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="4a598-182">Offers the option to register.</span></span>
  * <span data-ttu-id="4a598-183">ログインするオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="4a598-183">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="4a598-184">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4a598-184">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="4a598-185">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4a598-185">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="4a598-186">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="4a598-186">Run the app</span></span>

<span data-ttu-id="4a598-187">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4a598-187">Run the app from the Server project.</span></span> <span data-ttu-id="4a598-188">Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="4a598-188">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="4a598-189">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4a598-189">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
