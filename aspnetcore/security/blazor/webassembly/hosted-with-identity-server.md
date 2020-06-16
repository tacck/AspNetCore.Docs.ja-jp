---
title: Blazorサーバーを使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する Identity
author: guardrex
description: Blazorホスティング[サーバー](https://identityserver.io/)バックエンドを使用する Visual Studio 内から認証を使用して新しいホスト型アプリを作成するには
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: c85843c04688beefe7ea87d9e8b281d14ab85bc5
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776515"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="3d335-103">Blazorサーバーを使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する Identity</span><span class="sxs-lookup"><span data-stu-id="3d335-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="3d335-104">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3d335-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3d335-105">この記事では、 Blazor ユーザーと API 呼び出しを認証する[IdentityServer](https://identityserver.io/)ために、ユーザーを使用する新しいホスト型アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3d335-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3d335-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d335-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3d335-107">Visual Studio で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="3d335-107">In Visual Studio:</span></span>

1. <span data-ttu-id="3d335-108">\*\* Blazor 新しいアプリを作成し\*\*ます。</span><span class="sxs-lookup"><span data-stu-id="3d335-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="3d335-109">詳細については、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3d335-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="3d335-110">[**新しい Blazor アプリの作成**] ダイアログで、[**認証**] セクションの [**変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="3d335-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="3d335-111">**個々のユーザーアカウント**を選択し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3d335-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="3d335-112">[**詳細設定**] セクションで [ホストされている**ASP.NET Core** ] チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="3d335-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="3d335-113">**[作成]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3d335-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3d335-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3d335-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3d335-115">コマンドシェルでアプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3d335-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="3d335-116">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="3d335-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3d335-117">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="3d335-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="3d335-118">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="3d335-118">Server app configuration</span></span>

<span data-ttu-id="3d335-119">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="3d335-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="3d335-120">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="3d335-120">Startup class</span></span>

<span data-ttu-id="3d335-121">クラスには `Startup` 次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="3d335-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="3d335-122">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="3d335-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="3d335-123">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="3d335-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="3d335-124">サーバーには、次のように、 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 既定の ASP.NET Core 規則を設定するヘルパーメソッドが追加されています。</span><span class="sxs-lookup"><span data-stu-id="3d335-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="3d335-125">次のように追加のヘルパーメソッドを使用して認証し <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ます。これにより、アプリは、サービスによって生成された JWT トークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="3d335-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="3d335-126">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="3d335-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="3d335-127">サーバーミドルウェアは Open ID Connect (OIDC) エンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="3d335-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="3d335-128">認証ミドルウェアは、要求の資格情報を検証し、要求コンテキストでユーザーを設定する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="3d335-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="3d335-129">承認ミドルウェアにより、承認機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="3d335-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="3d335-130">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="3d335-130">AddApiAuthorization</span></span>

<span data-ttu-id="3d335-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>ヘルパーメソッドは、ASP.NET Core シナリオ用に[サーバー](https://identityserver.io/)を構成します。</span><span class="sxs-lookup"><span data-stu-id="3d335-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="3d335-132">サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="3d335-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="3d335-133">一般に、最も一般的なシナリオでは、不要な複雑さが公開されます。</span><span class="sxs-lookup"><span data-stu-id="3d335-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="3d335-134">そのため、適切な出発点として考えられる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3d335-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="3d335-135">認証を変更する必要がある場合は、アプリの要件に合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="3d335-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="3d335-136">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="3d335-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="3d335-137"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>ヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="3d335-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="3d335-138">ポリシーは、が URL 空間内のすべてのサブパスにルーティングされたすべての要求を処理できるように構成されてい Identity Identity `/Identity` ます。</span><span class="sxs-lookup"><span data-stu-id="3d335-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="3d335-139">は、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 他のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="3d335-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="3d335-140">さらに、このメソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3d335-140">Additionally, this method:</span></span>

* <span data-ttu-id="3d335-141">`{APPLICATION NAME}API`既定のスコープのを使用して、API リソースをに登録 `{APPLICATION NAME}API` します。</span><span class="sxs-lookup"><span data-stu-id="3d335-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="3d335-142">アプリに対して、サービスによって発行されたトークンを検証するように JWT ベアラートークンミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="3d335-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="3d335-143">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="3d335-143">WeatherForecastController</span></span>

<span data-ttu-id="3d335-144">`WeatherForecastController`(*Controllers/WeatherForecastController*) で、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="3d335-144">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="3d335-145">属性は、ユーザーがリソースにアクセスするための既定のポリシーに基づいて承認される必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="3d335-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="3d335-146">既定の承認ポリシーは、によって設定される既定の認証スキームを使用するように構成されてい <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="3d335-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="3d335-147">ヘルパーメソッドは、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> アプリに対する要求の既定のハンドラーとしてを構成します。</span><span class="sxs-lookup"><span data-stu-id="3d335-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="3d335-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="3d335-148">ApplicationDbContext</span></span>

<span data-ttu-id="3d335-149">`ApplicationDbContext`(*Data/ApplicationDbContext .cs*) では、を拡張して、をに <xref:Microsoft.EntityFrameworkCore.DbContext> 指定し <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> ます。</span><span class="sxs-lookup"><span data-stu-id="3d335-149">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="3d335-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="3d335-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="3d335-151">データベーススキーマを完全に制御するには、使用可能なクラスの1つを継承 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> し、メソッドでを呼び出してスキーマを含めるようにコンテキストを構成し Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="3d335-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="3d335-152">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="3d335-152">OidcConfigurationController</span></span>

<span data-ttu-id="3d335-153">`OidcConfigurationController`(*Controllers/OidcConfigurationController*) では、oidc パラメーターを提供するためにクライアントエンドポイントがプロビジョニングされます。</span><span class="sxs-lookup"><span data-stu-id="3d335-153">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="3d335-154">アプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="3d335-154">App settings files</span></span>

<span data-ttu-id="3d335-155">プロジェクトルートのアプリ設定ファイル (*appsettings.js*) では、 `IdentityServer` 構成されているクライアントの一覧がセクションに示されます。</span><span class="sxs-lookup"><span data-stu-id="3d335-155">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="3d335-156">次の例には、1つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="3d335-156">In the following example, there's a single client.</span></span> <span data-ttu-id="3d335-157">クライアント名はアプリケーション名に対応し、OAuth パラメーターに規約によってマップされ `ClientId` ます。</span><span class="sxs-lookup"><span data-stu-id="3d335-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="3d335-158">プロファイルは、構成されているアプリの種類を示します。</span><span class="sxs-lookup"><span data-stu-id="3d335-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="3d335-159">プロファイルは、サーバーの構成プロセスを簡略化する規則を推進するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="3d335-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="3d335-160">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名 (など `BlazorSample.Client` ) です。</span><span class="sxs-lookup"><span data-stu-id="3d335-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="3d335-161">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="3d335-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3d335-162">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="3d335-162">Authentication package</span></span>

<span data-ttu-id="3d335-163">個々のユーザーアカウント () を使用するようにアプリを作成すると、アプリは、アプリ `Individual` のプロジェクトファイルで[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="3d335-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="3d335-164">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3d335-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3d335-165">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="3d335-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="3d335-166">API 承認のサポート</span><span class="sxs-lookup"><span data-stu-id="3d335-166">API authorization support</span></span>

<span data-ttu-id="3d335-167">ユーザーを認証するためのサポートは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージ内に用意されている拡張メソッドによってサービスコンテナーに接続されます。</span><span class="sxs-lookup"><span data-stu-id="3d335-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="3d335-168">このメソッドは、既存の承認システムと対話するために、アプリが必要とするサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="3d335-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="3d335-169">既定では、アプリの構成はの規則によって読み込まれ `_configuration/{client-id}` ます。</span><span class="sxs-lookup"><span data-stu-id="3d335-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="3d335-170">規則により、クライアント ID はアプリのアセンブリ名に設定されます。</span><span class="sxs-lookup"><span data-stu-id="3d335-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="3d335-171">この URL は、オプションを指定してオーバーロードを呼び出すことによって、別のエンドポイントを指すように変更できます。</span><span class="sxs-lookup"><span data-stu-id="3d335-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="3d335-172">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="3d335-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="3d335-173">Index ページ</span><span class="sxs-lookup"><span data-stu-id="3d335-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="3d335-174">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="3d335-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="3d335-175">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3d335-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="3d335-176">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3d335-176">LoginDisplay component</span></span>

<span data-ttu-id="3d335-177">コンポーネント `LoginDisplay` (*Shared/logindisplay*) は、 `MainLayout` コンポーネント (*shared/mainlayout*) にレンダリングされ、次の動作を管理します。</span><span class="sxs-lookup"><span data-stu-id="3d335-177">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="3d335-178">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="3d335-178">For authenticated users:</span></span>
  * <span data-ttu-id="3d335-179">現在のユーザー名を表示します。</span><span class="sxs-lookup"><span data-stu-id="3d335-179">Displays the current user name.</span></span>
  * <span data-ttu-id="3d335-180">ASP.NET Core の [ユーザープロファイル] ページへのリンクを提供 Identity します。</span><span class="sxs-lookup"><span data-stu-id="3d335-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="3d335-181">アプリからログアウトするためのボタンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3d335-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="3d335-182">匿名ユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="3d335-182">For anonymous users:</span></span>
  * <span data-ttu-id="3d335-183">登録するオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3d335-183">Offers the option to register.</span></span>
  * <span data-ttu-id="3d335-184">ログインするオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3d335-184">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="3d335-185">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3d335-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="3d335-186">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3d335-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="3d335-187">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="3d335-187">Run the app</span></span>

<span data-ttu-id="3d335-188">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3d335-188">Run the app from the Server project.</span></span> <span data-ttu-id="3d335-189">Visual Studio を使用する場合は、次のいずれかの方法で行います。</span><span class="sxs-lookup"><span data-stu-id="3d335-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="3d335-190">ツールバーの [**スタートアッププロジェクト**] ドロップダウンリストを*サーバー API アプリ*に設定し、[**実行**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3d335-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="3d335-191">**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3d335-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="3d335-192">API 認証を使用した名前とロール要求</span><span class="sxs-lookup"><span data-stu-id="3d335-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="3d335-193">カスタムユーザーファクトリ</span><span class="sxs-lookup"><span data-stu-id="3d335-193">Custom user factory</span></span>

<span data-ttu-id="3d335-194">クライアントアプリで、カスタムユーザーファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="3d335-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="3d335-195">サーバーは、1つの要求で複数のロールを JSON 配列として送信 `role` します。</span><span class="sxs-lookup"><span data-stu-id="3d335-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="3d335-196">1つのロールが、要求の文字列値として送信されます。</span><span class="sxs-lookup"><span data-stu-id="3d335-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="3d335-197">ファクトリは、 `role` ユーザーのロールごとに個別の要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="3d335-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="3d335-198">*CustomUserFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="3d335-198">*CustomUserFactory.cs*:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="3d335-199">クライアントアプリで、ファクトリを `Program.Main` (*Program.cs*) に登録します。</span><span class="sxs-lookup"><span data-stu-id="3d335-199">In the Client app, register the factory in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="3d335-200">サーバーアプリで、ビルダーでを呼び出し <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity ます。これにより、ロールに関連するサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3d335-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="3d335-201">サーバーの構成 Identity</span><span class="sxs-lookup"><span data-stu-id="3d335-201">Configure Identity Server</span></span>

<span data-ttu-id="3d335-202">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="3d335-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="3d335-203">API 承認オプション</span><span class="sxs-lookup"><span data-stu-id="3d335-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="3d335-204">プロファイルサービス</span><span class="sxs-lookup"><span data-stu-id="3d335-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="3d335-205">API 承認オプション</span><span class="sxs-lookup"><span data-stu-id="3d335-205">API authorization options</span></span>

<span data-ttu-id="3d335-206">サーバーアプリで次のようにします。</span><span class="sxs-lookup"><span data-stu-id="3d335-206">In the Server app:</span></span>

* <span data-ttu-id="3d335-207">Identity `name` および `role` 要求を ID トークンとアクセストークンに配置するようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="3d335-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="3d335-208">JWT トークンハンドラーでロールの既定のマッピングを使用しないようにします。</span><span class="sxs-lookup"><span data-stu-id="3d335-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="3d335-209">プロファイルサービス</span><span class="sxs-lookup"><span data-stu-id="3d335-209">Profile Service</span></span>

<span data-ttu-id="3d335-210">サーバーアプリで、実装を作成 `ProfileService` します。</span><span class="sxs-lookup"><span data-stu-id="3d335-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="3d335-211">*ProfileService.cs*:</span><span class="sxs-lookup"><span data-stu-id="3d335-211">*ProfileService.cs*:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="3d335-212">サーバーアプリで、次のようにプロファイルサービスを登録し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="3d335-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="3d335-213">認証メカニズムを使用する</span><span class="sxs-lookup"><span data-stu-id="3d335-213">Use authorization mechanisms</span></span>

<span data-ttu-id="3d335-214">クライアントアプリでは、この時点でコンポーネントの承認方法が機能します。</span><span class="sxs-lookup"><span data-stu-id="3d335-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="3d335-215">コンポーネント内のすべての承認メカニズムは、ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="3d335-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="3d335-216">[Authorizeview コンポーネント](xref:security/blazor/index#authorizeview-component)(例: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="3d335-216">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="3d335-217">[ `[Authorize]` attribute ディレクティブ](xref:security/blazor/index#authorize-attribute)( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (例: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="3d335-217">[`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="3d335-218">[手続き型ロジック](xref:security/blazor/index#procedural-logic)(例: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="3d335-218">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="3d335-219">複数のロールテストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3d335-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="3d335-220">`User.Identity.Name`は、ユーザーのユーザー名を使用してクライアントアプリに設定されます。これは通常、サインイン電子メールアドレスです。</span><span class="sxs-lookup"><span data-stu-id="3d335-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3d335-221">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3d335-221">Additional resources</span></span>

* [<span data-ttu-id="3d335-222">Azure App Service への配置</span><span class="sxs-lookup"><span data-stu-id="3d335-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="3d335-223">Key Vault から証明書をインポートする (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="3d335-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="3d335-224">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="3d335-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
