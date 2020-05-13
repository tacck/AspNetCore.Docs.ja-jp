---
title: Blazorサーバーを使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する Identity
author: guardrex
description: Blazorホスティング[サーバー](https://identityserver.io/)バックエンドを使用する Visual Studio 内から認証を使用して新しいホスト型アプリを作成するには
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 2ab43ac5f4de398c57707de23a06a1650f6140cb
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153631"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="049c0-103">Blazorサーバーを使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する Identity</span><span class="sxs-lookup"><span data-stu-id="049c0-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="049c0-104">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="049c0-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="049c0-105">Blazorユーザーと API 呼び出しを認証するためにユーザーを使用[する、](https://identityserver.io/) Visual Studio で新しいホスト型アプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="049c0-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="049c0-106">Visual Studio を使用して、 \*\* Blazor 新しいアプリを作成し\*\*ます。</span><span class="sxs-lookup"><span data-stu-id="049c0-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="049c0-107">詳細については、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="049c0-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="049c0-108">[**新しい Blazor アプリの作成**] ダイアログで、[**認証**] セクションの [**変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="049c0-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="049c0-109">**個々のユーザーアカウント**を選択し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="049c0-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="049c0-110">[**詳細設定**] セクションで [ホストされている**ASP.NET Core** ] チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="049c0-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="049c0-111">**[作成]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="049c0-111">Select the **Create** button.</span></span>

<span data-ttu-id="049c0-112">コマンドシェルでアプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="049c0-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="049c0-113">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="049c0-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="049c0-114">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="049c0-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="049c0-115">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="049c0-115">Server app configuration</span></span>

<span data-ttu-id="049c0-116">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="049c0-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="049c0-117">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="049c0-117">Startup class</span></span>

<span data-ttu-id="049c0-118">クラスには `Startup` 、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="049c0-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="049c0-119">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="049c0-119">In `Startup.ConfigureServices`:</span></span>

  * Identity<span data-ttu-id="049c0-120">:</span><span class="sxs-lookup"><span data-stu-id="049c0-120">:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="049c0-121">サーバーには、次のように、 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 既定の ASP.NET Core 規則を設定する追加のヘルパーメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="049c0-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="049c0-122">次のように追加のヘルパーメソッドを使用して認証し <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ます。これにより、アプリは、サービスによって生成された JWT トークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="049c0-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="049c0-123">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="049c0-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="049c0-124">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="049c0-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="049c0-125">Open ID Connect (OIDC) エンドポイントを公開するサーバーミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="049c0-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="049c0-126">認証と承認のミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="049c0-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="049c0-127">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="049c0-127">AddApiAuthorization</span></span>

<span data-ttu-id="049c0-128"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>ヘルパーメソッドは、ASP.NET Core シナリオ用に[サーバー](https://identityserver.io/)を構成します。</span><span class="sxs-lookup"><span data-stu-id="049c0-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="049c0-129">サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="049c0-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="049c0-130">一般に、最も一般的なシナリオでは、不要な複雑さが公開されます。</span><span class="sxs-lookup"><span data-stu-id="049c0-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="049c0-131">そのため、適切な出発点として考えられる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="049c0-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="049c0-132">認証を変更する必要が生じた場合でも、アプリの要件に合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="049c0-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="049c0-133">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="049c0-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="049c0-134"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>ヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="049c0-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="049c0-135">ポリシーは、が URL 空間内のすべてのサブパスにルーティングされたすべての要求を処理できるように構成されてい Identity Identity `/Identity` ます。</span><span class="sxs-lookup"><span data-stu-id="049c0-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="049c0-136">は、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 他のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="049c0-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="049c0-137">さらに、このメソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="049c0-137">Additionally, this method:</span></span>

* <span data-ttu-id="049c0-138">`{APPLICATION NAME}API`既定のスコープのを使用して、API リソースをに登録 `{APPLICATION NAME}API` します。</span><span class="sxs-lookup"><span data-stu-id="049c0-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="049c0-139">アプリに対して、サービスによって発行されたトークンを検証するように JWT ベアラートークンミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="049c0-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="049c0-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="049c0-140">WeatherForecastController</span></span>

<span data-ttu-id="049c0-141">`WeatherForecastController`(*Controllers/WeatherForecastController*) で、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="049c0-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="049c0-142">属性は、ユーザーがリソースにアクセスするための既定のポリシーに基づいて承認される必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="049c0-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="049c0-143">既定の承認ポリシーは、既定の認証スキームを使用するように構成されます。これは、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 前に説明したポリシースキームによって設定されます。</span><span class="sxs-lookup"><span data-stu-id="049c0-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="049c0-144">ヘルパーメソッドは、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> アプリに対する要求の既定のハンドラーとしてを構成します。</span><span class="sxs-lookup"><span data-stu-id="049c0-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="049c0-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="049c0-145">ApplicationDbContext</span></span>

<span data-ttu-id="049c0-146">`ApplicationDbContext`(*Data/ApplicationDbContext .cs*) では、でも同じもの <xref:Microsoft.EntityFrameworkCore.DbContext> が使用され Identity ます。ただし、この例外は、を使用して、の <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> スキーマを追加します。</span><span class="sxs-lookup"><span data-stu-id="049c0-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="049c0-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="049c0-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="049c0-148">データベーススキーマを完全に制御するには、使用可能なクラスの1つを継承 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> し、メソッドでを呼び出してスキーマを含めるようにコンテキストを構成し Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` ます。</span><span class="sxs-lookup"><span data-stu-id="049c0-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="049c0-149">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="049c0-149">OidcConfigurationController</span></span>

<span data-ttu-id="049c0-150">`OidcConfigurationController`(*Controllers/OidcConfigurationController*) では、oidc パラメーターを提供するためにクライアントエンドポイントがプロビジョニングされます。</span><span class="sxs-lookup"><span data-stu-id="049c0-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="049c0-151">アプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="049c0-151">App settings files</span></span>

<span data-ttu-id="049c0-152">プロジェクトルートのアプリ設定ファイル (*appsettings. json*) で、 `IdentityServer` 構成されているクライアントの一覧を説明するセクションです。</span><span class="sxs-lookup"><span data-stu-id="049c0-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="049c0-153">次の例には、1つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="049c0-153">In the following example, there's a single client.</span></span> <span data-ttu-id="049c0-154">クライアント名はアプリケーション名に対応し、OAuth パラメーターに規約によってマップされ `ClientId` ます。</span><span class="sxs-lookup"><span data-stu-id="049c0-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="049c0-155">プロファイルは、構成されているアプリの種類を示します。</span><span class="sxs-lookup"><span data-stu-id="049c0-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="049c0-156">プロファイルは、サーバーの構成プロセスを簡略化する規則を推進するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="049c0-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="049c0-157">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="049c0-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="049c0-158">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="049c0-158">Authentication package</span></span>

<span data-ttu-id="049c0-159">個々のユーザーアカウント () を使用するようにアプリを作成すると、アプリはアプリの `Individual` プロジェクトファイルでパッケージのパッケージ参照を自動的に受け取り `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ます。</span><span class="sxs-lookup"><span data-stu-id="049c0-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="049c0-160">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="049c0-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="049c0-161">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="049c0-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="049c0-162">`{VERSION}`前のパッケージ参照のを、この記事に示されているパッケージのバージョンに置き換え `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> ます。</span><span class="sxs-lookup"><span data-stu-id="049c0-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="049c0-163">API 承認のサポート</span><span class="sxs-lookup"><span data-stu-id="049c0-163">API authorization support</span></span>

<span data-ttu-id="049c0-164">ユーザー認証のサポートは、パッケージ内で提供される拡張メソッドによってサービスコンテナーに接続され `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ます。</span><span class="sxs-lookup"><span data-stu-id="049c0-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="049c0-165">このメソッドは、アプリが既存の承認システムと対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="049c0-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="049c0-166">既定では、からの規則によって、アプリの構成が読み込ま `_configuration/{client-id}` れます。</span><span class="sxs-lookup"><span data-stu-id="049c0-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="049c0-167">規則により、クライアント ID はアプリのアセンブリ名に設定されます。</span><span class="sxs-lookup"><span data-stu-id="049c0-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="049c0-168">この URL は、オプションを指定してオーバーロードを呼び出すことによって、別のエンドポイントを指すように変更できます。</span><span class="sxs-lookup"><span data-stu-id="049c0-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="049c0-169">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="049c0-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="049c0-170">Index ページ</span><span class="sxs-lookup"><span data-stu-id="049c0-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="049c0-171">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="049c0-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="049c0-172">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="049c0-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="049c0-173">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="049c0-173">LoginDisplay component</span></span>

<span data-ttu-id="049c0-174">コンポーネント `LoginDisplay` (*Shared/logindisplay*) は、 `MainLayout` コンポーネント (*shared/mainlayout*) にレンダリングされ、次の動作を管理します。</span><span class="sxs-lookup"><span data-stu-id="049c0-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="049c0-175">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="049c0-175">For authenticated users:</span></span>
  * <span data-ttu-id="049c0-176">現在のユーザー名を表示します。</span><span class="sxs-lookup"><span data-stu-id="049c0-176">Displays the current user name.</span></span>
  * <span data-ttu-id="049c0-177">ASP.NET Core の [ユーザープロファイル] ページへのリンクを提供 Identity します。</span><span class="sxs-lookup"><span data-stu-id="049c0-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="049c0-178">アプリからログアウトするためのボタンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="049c0-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="049c0-179">匿名ユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="049c0-179">For anonymous users:</span></span>
  * <span data-ttu-id="049c0-180">登録するオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="049c0-180">Offers the option to register.</span></span>
  * <span data-ttu-id="049c0-181">ログインするオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="049c0-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="049c0-182">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="049c0-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="049c0-183">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="049c0-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="049c0-184">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="049c0-184">Run the app</span></span>

<span data-ttu-id="049c0-185">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="049c0-185">Run the app from the Server project.</span></span> <span data-ttu-id="049c0-186">Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="049c0-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="049c0-187">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="049c0-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="049c0-188">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="049c0-188">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
