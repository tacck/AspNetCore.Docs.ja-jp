---
title: ASP.NET Core Blazor WebAssembly でホストされているアプリを Identity Server でセキュリティ保護する
author: guardrex
description: '[IdentityServer](https://identityserver.io/) バックエンドを使用して、Visual Studio 内から認証を用いて新しい Blazor ホステッド アプリを作成するには'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 001fa0885c4ef4f365d9849278d3aa36e7657c54
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147728"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="3f33f-103">ASP.NET Core Blazor WebAssembly でホストされているアプリを Identity Server でセキュリティ保護する</span><span class="sxs-lookup"><span data-stu-id="3f33f-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="3f33f-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3f33f-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3f33f-105">この記事では、ユーザーと API 呼び出しの認証に [IdentityServer](https://identityserver.io/) を使用する、Blazor でホステッド アプリを新しく作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f33f-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f33f-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3f33f-107">認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="3f33f-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="3f33f-108">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor WebAssembly アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="3f33f-109">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納するには、 **[アプリ内のストア ユーザー アカウント]** オプションを使用して、 **[個人のユーザー アカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="3f33f-110">**[詳細設定]** セクションで、 **[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="3f33f-110">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="3f33f-111">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3f33f-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="3f33f-112">空のフォルダーに認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには、`-au|--auth` オプションを使用して `Individual` 認証メカニズムを指定し、ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-112">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="3f33f-113">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="3f33f-113">Placeholder</span></span>  | <span data-ttu-id="3f33f-114">例</span><span class="sxs-lookup"><span data-stu-id="3f33f-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="3f33f-115">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="3f33f-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="3f33f-116">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f33f-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3f33f-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3f33f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3f33f-118">認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="3f33f-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="3f33f-119">**[新しい Blazor WebAssembly アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="3f33f-120">ASP.NET Core の [Identity](xref:security/authentication/identity) を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="3f33f-121">**[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="3f33f-121">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="3f33f-122">サーバー アプリの構成</span><span class="sxs-lookup"><span data-stu-id="3f33f-122">Server app configuration</span></span>

<span data-ttu-id="3f33f-123">次のセクションでは、認証のサポートが含まれる場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-123">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="3f33f-124">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="3f33f-124">Startup class</span></span>

<span data-ttu-id="3f33f-125">`Startup` クラスには次のものが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-125">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="3f33f-126">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="3f33f-126">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="3f33f-127">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="3f33f-127">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity<span data-ttu-id="3f33f-128">Server 上に既定の ASP.NET Core 規則を設定する <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドが追加された IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="3f33f-128">Server with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="3f33f-129">IdentityServer によって生成された JWT トークンを検証するようにアプリを構成する <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ヘルパー メソッドが追加された Authentication:</span><span class="sxs-lookup"><span data-stu-id="3f33f-129">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="3f33f-130">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="3f33f-130">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="3f33f-131">IdentityServer のミドルウェアでは、Open ID Connect (OIDC) のエンドポイントが公開されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-131">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="3f33f-132">認証ミドルウェアでは、要求の資格情報の検証と、要求コンテキストでのユーザーの設定が行われます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-132">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="3f33f-133">承認ミドルウェアにより、承認機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="3f33f-133">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="3f33f-134">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="3f33f-134">AddApiAuthorization</span></span>

<span data-ttu-id="3f33f-135"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドでは、ASP.NET Core シナリオ対応に [IdentityServer](https://identityserver.io/) が構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-135">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> Identity<span data-ttu-id="3f33f-136">Server は、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="3f33f-136">Server is a powerful and extensible framework for handling app security concerns.</span></span> Identity<span data-ttu-id="3f33f-137">Server を使用すると、ほとんどの一般的なシナリオには必要のない複雑さが発生します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-137">Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="3f33f-138">そのため、使用開始時に適切であると考えられる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3f33f-138">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="3f33f-139">認証のニーズが変わったら、IdentityServer のあらゆる機能を利用し、アプリの要件に合わせて認証をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-139">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="3f33f-140">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="3f33f-140">AddIdentityServerJwt</span></span>

<span data-ttu-id="3f33f-141"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ヘルパー メソッドでは、アプリに対するポリシー スキームが既定の認証ハンドラーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-141">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="3f33f-142">そのポリシーは、Identity の URL 空間 `/Identity` のサブパスにルーティングされたすべての要求を Identity で処理できるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="3f33f-142">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="3f33f-143">それ以外のすべての要求は、<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> で処理されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-143">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="3f33f-144">さらに、このメソッドでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-144">Additionally, this method:</span></span>

* <span data-ttu-id="3f33f-145">既定のスコープ `{APPLICATION NAME}API` を使用して、`{APPLICATION NAME}API` API リソースが IdentityServer に登録されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-145">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="3f33f-146">IdentityServer によってアプリに対して発行されたトークンを検証するように、JWT ベアラー トークン ミドルウェアが構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-146">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="3f33f-147">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="3f33f-147">WeatherForecastController</span></span>

<span data-ttu-id="3f33f-148">`WeatherForecastController` (`Controllers/WeatherForecastController.cs`) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-148">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="3f33f-149">その属性では、ユーザーはリソースにアクセスするために既定のポリシーに基づいて承認される必要があることが示されています。</span><span class="sxs-lookup"><span data-stu-id="3f33f-149">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="3f33f-150">既定の承認ポリシーは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> によって設定される既定の認証スキームを使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="3f33f-150">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="3f33f-151">ヘルパー メソッドでは、アプリへの要求に対する既定のハンドラーとして <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> が構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-151">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="3f33f-152">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="3f33f-152">ApplicationDbContext</span></span>

<span data-ttu-id="3f33f-153">`ApplicationDbContext` (`Data/ApplicationDbContext.cs`) では、<xref:Microsoft.EntityFrameworkCore.DbContext> により、IdentityServer 用のスキーマを含むように <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> が拡張されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-153">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="3f33f-154"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="3f33f-154"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="3f33f-155">データベース スキーマを完全に制御するには、使用可能な Identity <xref:Microsoft.EntityFrameworkCore.DbContext> クラスの 1 つを継承し、<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> メソッドで `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` を呼び出すことによって、Identity スキーマを含むようにコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-155">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="3f33f-156">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="3f33f-156">OidcConfigurationController</span></span>

<span data-ttu-id="3f33f-157">`OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) では、OIDC パラメーターを提供するために、クライアント エンドポイントがプロビジョニングされます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-157">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="3f33f-158">アプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="3f33f-158">App settings files</span></span>

<span data-ttu-id="3f33f-159">プロジェクト ルートにあるアプリ設定ファイル (`appsettings.json`) の `IdentityServer` セクションには、構成されているクライアントの一覧が記述されてます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-159">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="3f33f-160">次の例には、1 つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="3f33f-160">In the following example, there's a single client.</span></span> <span data-ttu-id="3f33f-161">クライアント名はアプリケーション名に対応し、規則によって OAuth の `ClientId` パラメーターにマップされます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-161">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="3f33f-162">構成対象のアプリの種類は、プロファイルによって示されています。</span><span class="sxs-lookup"><span data-stu-id="3f33f-162">The profile indicates the app type being configured.</span></span> <span data-ttu-id="3f33f-163">プロファイルは、サーバーの構成プロセスを簡素化する規則を促進するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-163">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="3f33f-164">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample.Client`)。</span><span class="sxs-lookup"><span data-stu-id="3f33f-164">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="3f33f-165">クライアント アプリの構成</span><span class="sxs-lookup"><span data-stu-id="3f33f-165">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3f33f-166">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="3f33f-166">Authentication package</span></span>

<span data-ttu-id="3f33f-167">個人のユーザー アカウント (`Individual`) を使用するようにアプリを作成すると、アプリはそのアプリのプロジェクト ファイル内で [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="3f33f-167">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="3f33f-168">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3f33f-168">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3f33f-169">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-169">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="3f33f-170">API の承認のサポート</span><span class="sxs-lookup"><span data-stu-id="3f33f-170">API authorization support</span></span>

<span data-ttu-id="3f33f-171">ユーザーの認証に対するサポートは、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージ内で提供される拡張メソッドによって、サービス コンテナーに接続されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-171">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="3f33f-172">このメソッドでは、アプリが既存の承認システムとやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-172">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="3f33f-173">既定では、アプリの構成は規則により `_configuration/{client-id}` から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-173">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="3f33f-174">規則により、アプリのアセンブリ名にはクライアント ID が設定されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-174">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="3f33f-175">オプションを指定してオーバーロードを呼び出すことにより、別のエンドポイントを指すようにこの URL を変更できます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-175">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="3f33f-176">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="3f33f-176">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="3f33f-177">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="3f33f-177">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="3f33f-178">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3f33f-178">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="3f33f-179">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3f33f-179">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="3f33f-180">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3f33f-180">LoginDisplay component</span></span>

<span data-ttu-id="3f33f-181">`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-181">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="3f33f-182">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="3f33f-182">For authenticated users:</span></span>
  * <span data-ttu-id="3f33f-183">現在のユーザー名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-183">Displays the current user name.</span></span>
  * <span data-ttu-id="3f33f-184">ASP.NET Core Identity のユーザー プロファイル ページへのリンクが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-184">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="3f33f-185">アプリからログアウトするためのボタンが用意されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-185">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="3f33f-186">匿名ユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="3f33f-186">For anonymous users:</span></span>
  * <span data-ttu-id="3f33f-187">登録するオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-187">Offers the option to register.</span></span>
  * <span data-ttu-id="3f33f-188">ログインするオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-188">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="3f33f-189">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3f33f-189">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="3f33f-190">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3f33f-190">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="3f33f-191">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="3f33f-191">Run the app</span></span>

<span data-ttu-id="3f33f-192">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-192">Run the app from the Server project.</span></span> <span data-ttu-id="3f33f-193">Visual Studio を使用しているときは、次のいずれかを行います。</span><span class="sxs-lookup"><span data-stu-id="3f33f-193">When using Visual Studio, either:</span></span>

* <span data-ttu-id="3f33f-194">ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを "*サーバー API アプリ*" に設定して、 **[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-194">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="3f33f-195">**ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-195">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="3f33f-196">API 認証での名前とロール要求</span><span class="sxs-lookup"><span data-stu-id="3f33f-196">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="3f33f-197">カスタム ユーザー ファクトリ</span><span class="sxs-lookup"><span data-stu-id="3f33f-197">Custom user factory</span></span>

<span data-ttu-id="3f33f-198">クライアント アプリで、カスタム ユーザー ファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-198">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="3f33f-199"> Server により、複数のロールが JSON 配列として 1 つの `role` 要求で送信されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-199"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="3f33f-200">1 つのロールは、要求内で文字列値として送信されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-200">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="3f33f-201">ファクトリにより、ユーザーのロールごとに個別の `role` 要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-201">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="3f33f-202">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="3f33f-202">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="3f33f-203">クライアント アプリでは、`Program.Main` (`Program.cs`) にファクトリを登録します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-203">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="3f33f-204">サーバー アプリでは、Identity ビルダーで <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> を呼び出します。これにより、ロールに関連するサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-204">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="3f33f-205">Identity Server を構成する</span><span class="sxs-lookup"><span data-stu-id="3f33f-205">Configure Identity Server</span></span>

<span data-ttu-id="3f33f-206">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-206">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="3f33f-207">API 承認のオプション</span><span class="sxs-lookup"><span data-stu-id="3f33f-207">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="3f33f-208">プロファイル サービス</span><span class="sxs-lookup"><span data-stu-id="3f33f-208">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="3f33f-209">API 承認のオプション</span><span class="sxs-lookup"><span data-stu-id="3f33f-209">API authorization options</span></span>

<span data-ttu-id="3f33f-210">サーバー アプリで次のようにします。</span><span class="sxs-lookup"><span data-stu-id="3f33f-210">In the Server app:</span></span>

* <span data-ttu-id="3f33f-211">`name` 要求と `role` 要求を ID トークンとアクセス トークンに挿入するように、Identity Server を構成します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-211">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="3f33f-212">JWT トークン ハンドラーでロールの既定のマッピングを禁止します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-212">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="3f33f-213">プロファイル サービス</span><span class="sxs-lookup"><span data-stu-id="3f33f-213">Profile Service</span></span>

<span data-ttu-id="3f33f-214">サーバー アプリで、`ProfileService` の実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-214">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="3f33f-215">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="3f33f-215">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="3f33f-216">サーバー アプリで、`Startup.ConfigureServices` にプロファイル サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="3f33f-216">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="3f33f-217">承認メカニズムを使用する</span><span class="sxs-lookup"><span data-stu-id="3f33f-217">Use authorization mechanisms</span></span>

<span data-ttu-id="3f33f-218">クライアント アプリでは、この時点でコンポーネントの承認方法が機能しています。</span><span class="sxs-lookup"><span data-stu-id="3f33f-218">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="3f33f-219">コンポーネント内のすべての承認メカニズムで、ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="3f33f-219">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="3f33f-220">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component) (例: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="3f33f-220">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="3f33f-221">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (例: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="3f33f-221">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="3f33f-222">[手続き型ロジック](xref:blazor/security/index#procedural-logic) (例: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="3f33f-222">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="3f33f-223">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3f33f-223">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="3f33f-224">`User.Identity.Name` には、クライアント アプリにおいてユーザーのユーザー名が設定されます。これは通常、サインイン メール アドレスです。</span><span class="sxs-lookup"><span data-stu-id="3f33f-224">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3f33f-225">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3f33f-225">Additional resources</span></span>

* [<span data-ttu-id="3f33f-226">Azure App Service にデプロイする</span><span class="sxs-lookup"><span data-stu-id="3f33f-226">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="3f33f-227">Key Vault から証明書をインポートする (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="3f33f-227">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="3f33f-228">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="3f33f-228">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
