---
title: 'ホストされている ASP.NET Core :::no-loc(Blazor WebAssembly)::: アプリを :::no-loc(Identity)::: Server でセキュリティ保護する'
author: guardrex
description: 'ホストされている ASP.NET Core :::no-loc(Blazor WebAssembly)::: アプリを :::no-loc(Identity)::: Server でセキュリティ保護する方法について説明します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 147f1d6cdea0b9992b8be333db4cb06e30c7feaf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055218"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="6fc64-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: でホストされているアプリを :::no-loc(Identity)::: Server でセキュリティ保護する</span><span class="sxs-lookup"><span data-stu-id="6fc64-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="6fc64-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6fc64-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6fc64-105">この記事では、ユーザーと API 呼び出しの認証に [:::no-loc(Identity):::Server](https://identityserver.io/) を使用する、[ホステッド :::no-loc(Blazor WebAssembly)::: アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-105">This article explains how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [:::no-loc(Identity):::Server](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="6fc64-106">既存の外部 :::no-loc(Identity)::: サーバー インスタンスを使用するように、スタンドアロンの、またはホストされた :::no-loc(Blazor WebAssembly)::: アプリを構成するには、<xref:blazor/security/webassembly/standalone-with-authentication-library> のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="6fc64-106">To configure a standalone or hosted :::no-loc(Blazor WebAssembly)::: app to use an existing, external :::no-loc(Identity)::: Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6fc64-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6fc64-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6fc64-108">認証メカニズムを使用して新しい :::no-loc(Blazor WebAssembly)::: プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="6fc64-108">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="6fc64-109">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[:::no-loc(Blazor WebAssembly)::: アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-109">After choosing the **:::no-loc(Blazor WebAssembly)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="6fc64-110">ASP.NET Core の [:::no-loc(Identity):::](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納するには、 **[アプリ内のストア ユーザー アカウント]** オプションを使用して、 **[個人のユーザー アカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="6fc64-111">**[詳細設定]** セクションで、 **[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6fc64-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="6fc64-112">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6fc64-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="6fc64-113">空のフォルダーに認証メカニズムを使用して新しい :::no-loc(Blazor WebAssembly)::: プロジェクトを作成するには、`-au|--auth` オプションを使用して `Individual` 認証メカニズムを指定し、ASP.NET Core の [:::no-loc(Identity):::](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-113">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="6fc64-114">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="6fc64-114">Placeholder</span></span>  | <span data-ttu-id="6fc64-115">例</span><span class="sxs-lookup"><span data-stu-id="6fc64-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `:::no-loc(Blazor):::Sample` |

<span data-ttu-id="6fc64-116">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="6fc64-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="6fc64-117">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6fc64-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6fc64-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6fc64-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6fc64-119">認証メカニズムを使用して新しい :::no-loc(Blazor WebAssembly)::: プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="6fc64-119">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="6fc64-120">**[新しい :::no-loc(Blazor WebAssembly)::: アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-120">On the **Configure your new :::no-loc(Blazor WebAssembly)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="6fc64-121">ASP.NET Core の [:::no-loc(Identity):::](xref:security/authentication/identity) を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-121">The app is created for individual users stored in the app with ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="6fc64-122">**[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6fc64-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="6fc64-123">*`Server`* アプリの構成</span><span class="sxs-lookup"><span data-stu-id="6fc64-123">*`Server`* app configuration</span></span>

<span data-ttu-id="6fc64-124">次のセクションでは、認証のサポートが含まれる場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="6fc64-125">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="6fc64-125">Startup class</span></span>

<span data-ttu-id="6fc64-126">`Startup` クラスには次のものが追加されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="6fc64-127">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="6fc64-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="6fc64-128">:::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="6fc64-128">:::no-loc(ASP.NET Core Identity)::::</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="6fc64-129">:::no-loc(Identity):::Server 上に既定の ASP.NET Core 規則を設定する <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドが追加された :::no-loc(Identity):::Server:</span><span class="sxs-lookup"><span data-stu-id="6fc64-129">:::no-loc(Identity):::Server with an additional <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.Add:::no-loc(Identity):::Server()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="6fc64-130">:::no-loc(Identity):::Server によって生成された JWT トークンを検証するようにアプリを構成する <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> ヘルパー メソッドが追加された Authentication:</span><span class="sxs-lookup"><span data-stu-id="6fc64-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method that configures the app to validate JWT tokens produced by :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.AddAuthentication()
        .Add:::no-loc(Identity):::ServerJwt();
    ```

* <span data-ttu-id="6fc64-131">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="6fc64-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="6fc64-132">:::no-loc(Identity):::Server のミドルウェアによって、OpenID Connect (OIDC) のエンドポイントが公開されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-132">The :::no-loc(Identity):::Server middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.Use:::no-loc(Identity):::Server();
    ```

  * <span data-ttu-id="6fc64-133">認証ミドルウェアでは、要求の資格情報の検証と、要求コンテキストでのユーザーの設定が行われます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="6fc64-134">承認ミドルウェアにより、承認機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="6fc64-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="6fc64-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="6fc64-135">AddApiAuthorization</span></span>

<span data-ttu-id="6fc64-136"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドでは、ASP.NET Core シナリオ対応に [:::no-loc(Identity):::Server](https://identityserver.io/) が構成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-136">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [:::no-loc(Identity):::Server](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="6fc64-137">:::no-loc(Identity):::Server は、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="6fc64-137">:::no-loc(Identity):::Server is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="6fc64-138">:::no-loc(Identity):::Server を使用すると、ほとんどの一般的なシナリオには必要のない複雑さが発生します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-138">:::no-loc(Identity):::Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="6fc64-139">そのため、使用開始時に適切であると考えられる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="6fc64-140">認証のニーズが変わったら、:::no-loc(Identity):::Server のあらゆる機能を利用し、アプリの要件に合わせて認証をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-140">Once your authentication needs change, the full power of :::no-loc(Identity):::Server is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="6fc64-141">Add:::no-loc(Identity):::ServerJwt</span><span class="sxs-lookup"><span data-stu-id="6fc64-141">Add:::no-loc(Identity):::ServerJwt</span></span>

<span data-ttu-id="6fc64-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> ヘルパー メソッドでは、アプリに対するポリシー スキームが既定の認証ハンドラーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="6fc64-143">そのポリシーは、:::no-loc(Identity)::: の URL 空間 `/:::no-loc(Identity):::` のサブパスにルーティングされたすべての要求を :::no-loc(Identity)::: で処理できるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-143">The policy is configured to allow :::no-loc(Identity)::: to handle all requests routed to any subpath in the :::no-loc(Identity)::: URL space `/:::no-loc(Identity):::`.</span></span> <span data-ttu-id="6fc64-144">それ以外のすべての要求は、<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> で処理されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="6fc64-145">さらに、このメソッドでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-145">Additionally, this method:</span></span>

* <span data-ttu-id="6fc64-146">既定のスコープ `{APPLICATION NAME}API` を使用して、`{APPLICATION NAME}API` API リソースが :::no-loc(Identity):::Server に登録されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-146">Registers an `{APPLICATION NAME}API` API resource with :::no-loc(Identity):::Server with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="6fc64-147">:::no-loc(Identity):::Server によってアプリに対して発行されたトークンを検証するように、JWT ベアラー トークン ミドルウェアが構成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-147">Configures the JWT Bearer Token Middleware to validate tokens issued by :::no-loc(Identity):::Server for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="6fc64-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="6fc64-148">WeatherForecastController</span></span>

<span data-ttu-id="6fc64-149">`WeatherForecastController` (`Controllers/WeatherForecastController.cs`) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="6fc64-150">その属性では、ユーザーはリソースにアクセスするために既定のポリシーに基づいて承認される必要があることが示されています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="6fc64-151">既定の承認ポリシーは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> によって設定される既定の認証スキームを使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>.</span></span> <span data-ttu-id="6fc64-152">ヘルパー メソッドでは、アプリへの要求に対する既定のハンドラーとして <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> が構成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="6fc64-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="6fc64-153">ApplicationDbContext</span></span>

<span data-ttu-id="6fc64-154">`ApplicationDbContext` (`Data/ApplicationDbContext.cs`) では、<xref:Microsoft.EntityFrameworkCore.DbContext> により、:::no-loc(Identity):::Server 用のスキーマを含むように <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> が拡張されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> to include the schema for :::no-loc(Identity):::Server.</span></span> <span data-ttu-id="6fc64-155"><xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-155"><xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext>.</span></span>

<span data-ttu-id="6fc64-156">データベース スキーマを完全に制御するには、使用可能な :::no-loc(Identity)::: <xref:Microsoft.EntityFrameworkCore.DbContext> クラスの 1 つを継承し、<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> メソッドで `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` を呼び出すことによって、:::no-loc(Identity)::: スキーマを含むようにコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-156">To gain full control of the database schema, inherit from one of the available :::no-loc(Identity)::: <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the :::no-loc(Identity)::: schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="6fc64-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="6fc64-157">OidcConfigurationController</span></span>

<span data-ttu-id="6fc64-158">`OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) では、OIDC パラメーターを提供するために、クライアント エンドポイントがプロビジョニングされます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="6fc64-159">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="6fc64-159">App settings</span></span>

<span data-ttu-id="6fc64-160">プロジェクト ルートにあるアプリ設定ファイル (`:::no-loc(appsettings.json):::`) の `:::no-loc(Identity):::Server` セクションには、構成されているクライアントの一覧が記述されてます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-160">In the app settings file (`:::no-loc(appsettings.json):::`) at the project root, the `:::no-loc(Identity):::Server` section describes the list of configured clients.</span></span> <span data-ttu-id="6fc64-161">次の例には、1 つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="6fc64-161">In the following example, there's a single client.</span></span> <span data-ttu-id="6fc64-162">クライアント名はアプリケーション名に対応し、規則によって OAuth の `ClientId` パラメーターにマップされます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="6fc64-163">構成対象のアプリの種類は、プロファイルによって示されています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="6fc64-164">プロファイルは、サーバーの構成プロセスを簡素化する規則を促進するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": ":::no-loc(Identity):::ServerSPA"
    }
  }
}
```

<span data-ttu-id="6fc64-165">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `:::no-loc(Blazor):::Sample.Client`)。</span><span class="sxs-lookup"><span data-stu-id="6fc64-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="6fc64-166">*`Client`* アプリの構成</span><span class="sxs-lookup"><span data-stu-id="6fc64-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6fc64-167">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="6fc64-167">Authentication package</span></span>

<span data-ttu-id="6fc64-168">個人のユーザー アカウント (`Individual`) を使用するようにアプリを作成すると、アプリはそのアプリのプロジェクト ファイル内で [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6fc64-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="6fc64-169">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6fc64-170">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="6fc64-171">プレースホルダー `{VERSION}` では、 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="6fc64-172">`HttpClient` 構成</span><span class="sxs-lookup"><span data-stu-id="6fc64-172">`HttpClient` configuration</span></span>

<span data-ttu-id="6fc64-173">`Program.Main` (`Program.cs`) では、サーバー API への要求を行うときのアクセス トークンが含まれる <xref:System.Net.Http.HttpClient> インスタンスを提供するように、名前付きの <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) が構成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="6fc64-174">ホストされている :::no-loc(Blazor):::ソリューションの一部ではない既存の :::no-loc(Identity)::: Server インスタンスを使用するように :::no-loc(Blazor WebAssembly)::: アプリを構成する場合は、<xref:System.Net.Http.HttpClient> ベース アドレスの登録を <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) からサーバー アプリの API 認証エンドポイント URL に変更します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-174">If you're configuring a :::no-loc(Blazor WebAssembly)::: app to use an existing :::no-loc(Identity)::: Server instance that isn't part of a hosted :::no-loc(Blazor)::: solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="6fc64-175">API の承認のサポート</span><span class="sxs-lookup"><span data-stu-id="6fc64-175">API authorization support</span></span>

<span data-ttu-id="6fc64-176">ユーザーの認証に対するサポートは、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージ内で提供される拡張メソッドによって、サービス コンテナーに接続されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="6fc64-177">このメソッドでは、アプリが既存の承認システムとやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="6fc64-178">既定では、アプリの構成は規則により `_configuration/{client-id}` から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="6fc64-179">規則により、アプリのアセンブリ名にはクライアント ID が設定されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="6fc64-180">オプションを指定してオーバーロードを呼び出すことにより、別のエンドポイントを指すようにこの URL を変更できます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="6fc64-181">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="6fc64-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="6fc64-182">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="6fc64-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="6fc64-183">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6fc64-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="6fc64-184">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6fc64-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="6fc64-185">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6fc64-185">LoginDisplay component</span></span>

<span data-ttu-id="6fc64-186">`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="6fc64-187">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="6fc64-187">For authenticated users:</span></span>
  * <span data-ttu-id="6fc64-188">現在のユーザー名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-188">Displays the current user name.</span></span>
  * <span data-ttu-id="6fc64-189">:::no-loc(ASP.NET Core Identity)::: のユーザー プロファイル ページへのリンクが提供されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-189">Offers a link to the user profile page in :::no-loc(ASP.NET Core Identity):::.</span></span>
  * <span data-ttu-id="6fc64-190">アプリからログアウトするためのボタンが用意されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="6fc64-191">匿名ユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="6fc64-191">For anonymous users:</span></span>
  * <span data-ttu-id="6fc64-192">登録するオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-192">Offers the option to register.</span></span>
  * <span data-ttu-id="6fc64-193">ログインするオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.:::no-loc(Identity):::.Name!</a>
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

### <a name="authentication-component"></a><span data-ttu-id="6fc64-194">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6fc64-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="6fc64-195">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6fc64-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="6fc64-196">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="6fc64-196">Run the app</span></span>

<span data-ttu-id="6fc64-197">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-197">Run the app from the Server project.</span></span> <span data-ttu-id="6fc64-198">Visual Studio を使用しているときは、次のいずれかを行います。</span><span class="sxs-lookup"><span data-stu-id="6fc64-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="6fc64-199">ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを " *サーバー API アプリ* " に設定して、 **[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="6fc64-200">**ソリューション エクスプローラー** でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="6fc64-201">API 認証での名前とロール要求</span><span class="sxs-lookup"><span data-stu-id="6fc64-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="6fc64-202">カスタム ユーザー ファクトリ</span><span class="sxs-lookup"><span data-stu-id="6fc64-202">Custom user factory</span></span>

<span data-ttu-id="6fc64-203">*`Client`* アプリで、カスタム ユーザー ファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="6fc64-204">:::no-loc(Identity)::: Server により、複数のロールが JSON 配列として 1 つの `role` 要求で送信されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-204">:::no-loc(Identity)::: Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="6fc64-205">1 つのロールは、要求内で文字列値として送信されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="6fc64-206">ファクトリにより、ユーザーのロールごとに個別の `role` 要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="6fc64-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="6fc64-207">`CustomUserFactory.cs`:</span></span>

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

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            var identity = (Claims:::no-loc(Identity):::)user.:::no-loc(Identity):::;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

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

<span data-ttu-id="6fc64-208">*`Client`* アプリでは、`Program.Main` (`Program.cs`) にファクトリを登録します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="6fc64-209">*`Server`* アプリでは、:::no-loc(Identity)::: ビルダーで <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> を呼び出します。これにより、ロールに関連するサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> on the :::no-loc(Identity)::: builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::;

...

services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<:::no-loc(Identity):::Role>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="6fc64-210">:::no-loc(Identity)::: Server を構成する</span><span class="sxs-lookup"><span data-stu-id="6fc64-210">Configure :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="6fc64-211">次の方法の **いずれか** を使用します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="6fc64-212">API 承認のオプション</span><span class="sxs-lookup"><span data-stu-id="6fc64-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="6fc64-213">プロファイル サービス</span><span class="sxs-lookup"><span data-stu-id="6fc64-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="6fc64-214">API 承認のオプション</span><span class="sxs-lookup"><span data-stu-id="6fc64-214">API authorization options</span></span>

<span data-ttu-id="6fc64-215">*`Server`* アプリで:</span><span class="sxs-lookup"><span data-stu-id="6fc64-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="6fc64-216">`name` 要求と `role` 要求を ID トークンとアクセス トークンに挿入するように、:::no-loc(Identity)::: Server を構成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-216">Configure :::no-loc(Identity)::: Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="6fc64-217">JWT トークン ハンドラーでロールの既定のマッピングを禁止します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Linq;

...

services.Add:::no-loc(Identity):::Server()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="6fc64-218">プロファイル サービス</span><span class="sxs-lookup"><span data-stu-id="6fc64-218">Profile Service</span></span>

<span data-ttu-id="6fc64-219">*`Server`* アプリで、`ProfileService` の実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="6fc64-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="6fc64-220">`ProfileService.cs`:</span></span>

```csharp
using :::no-loc(Identity):::Model;
using :::no-loc(Identity):::Server4.Models;
using :::no-loc(Identity):::Server4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="6fc64-221">*`Server`* アプリで、`Startup.ConfigureServices` にプロファイル サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using :::no-loc(Identity):::Server4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="6fc64-222">承認メカニズムを使用する</span><span class="sxs-lookup"><span data-stu-id="6fc64-222">Use authorization mechanisms</span></span>

<span data-ttu-id="6fc64-223">*`Client`* アプリでは、この時点でコンポーネントの承認方法が機能しています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="6fc64-224">コンポーネント内のすべての承認メカニズムで、ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="6fc64-225">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component) (例: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="6fc64-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="6fc64-226">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (例: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="6fc64-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="6fc64-227">[手続き型ロジック](xref:blazor/security/index#procedural-logic) (例: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="6fc64-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="6fc64-228">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="6fc64-229">`User.:::no-loc(Identity):::.Name` には、 *`Client`* アプリにおいてユーザーのユーザー名が設定されます。これは通常、サインイン メール アドレスです。</span><span class="sxs-lookup"><span data-stu-id="6fc64-229">`User.:::no-loc(Identity):::.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="6fc64-230">カスタム ドメインを使用した Azure App Service でのホスト</span><span class="sxs-lookup"><span data-stu-id="6fc64-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="6fc64-231">次のガイダンスでは、:::no-loc(Identity)::: Server を使用してホストされている :::no-loc(Blazor WebAssembly)::: アプリを、カスタム ドメインを使用して [Azure App Service](https://azure.microsoft.com/services/app-service/) にデプロイする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-231">The following guidance explains how to deploy a hosted :::no-loc(Blazor WebAssembly)::: app with :::no-loc(Identity)::: Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="6fc64-232">このホスティング シナリオの場合、 [:::no-loc(Identity)::: Server のトークン署名キー](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation)と、サイトの HTTPS でセキュリティ保護されたブラウザーとの通信に、同じ証明書を **使用しないでください** 。</span><span class="sxs-lookup"><span data-stu-id="6fc64-232">For this hosting scenario, do **not** use the same certificate for [:::no-loc(Identity)::: Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="6fc64-233">この 2 つの要件に対して異なる証明書を使用することは、それぞれの目的に対して秘密キーが分離されるため、優れたセキュリティ方法です。</span><span class="sxs-lookup"><span data-stu-id="6fc64-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="6fc64-234">ブラウザーとの通信に使用される TLS 証明書は、:::no-loc(Identity)::: Server のトークン署名に影響を与えることなく、個別に管理されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-234">TLS certificates for communication with browsers is managed independently without affecting :::no-loc(Identity)::: Server's token signing.</span></span>
* <span data-ttu-id="6fc64-235">カスタム ドメイン バインドのために [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) で App Service アプリに証明書を提供すると、:::no-loc(Identity)::: Server で Azure Key Vault からトークン署名用に同じ証明書を取得することはできません。</span><span class="sxs-lookup"><span data-stu-id="6fc64-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, :::no-loc(Identity)::: Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="6fc64-236">物理パスから同じ TLS 証明書を使用するように :::no-loc(Identity)::: Server を構成することはできますが、セキュリティ証明書をソース管理に置くことは **不適切な方法であり、ほとんどのシナリオで避ける必要があります** 。</span><span class="sxs-lookup"><span data-stu-id="6fc64-236">Although configuring :::no-loc(Identity)::: Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="6fc64-237">次のガイダンスでは、自己署名証明書は :::no-loc(Identity)::: Server のトークン署名のためだけに Azure Key Vault に作成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="6fc64-238">:::no-loc(Identity)::: Server の構成により、アプリの `My` > `CurrentUser` 証明書ストアを介して、キー コンテナー証明書が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-238">The :::no-loc(Identity)::: Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="6fc64-239">カスタム ドメインで HTTPS トラフィックに使用される他の証明書は、:::no-loc(Identity)::: Server の署名証明書とは別に作成および構成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the :::no-loc(Identity)::: Server signing certificate.</span></span>

<span data-ttu-id="6fc64-240">カスタム ドメインと HTTPS を使用してホストするようにアプリ、Azure App Service、Azure Key Vault を構成するには:</span><span class="sxs-lookup"><span data-stu-id="6fc64-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="6fc64-241">プラン レベル `Basic B1` 以上を使用して、[App Service プラン](/azure/app-service/overview-hosting-plans)を作成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="6fc64-242">App Service でカスタム ドメインを使用するには、`Basic B1` 以上のサービス レベルが必要です。</span><span class="sxs-lookup"><span data-stu-id="6fc64-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="6fc64-243">組織で管理するサイトの完全修飾ドメイン名 (FQDN) の共通名を使用して (例: `www.contoso.com`)、サイトのセキュリティで保護されたブラウザー通信 (HTTPS プロトコル) 用の PFX 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="6fc64-244">次のように証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-244">Create the certificate with:</span></span>
   * <span data-ttu-id="6fc64-245">キーで使用</span><span class="sxs-lookup"><span data-stu-id="6fc64-245">Key uses</span></span>
     * <span data-ttu-id="6fc64-246">デジタル署名の検証 (`digitalSignature`)</span><span class="sxs-lookup"><span data-stu-id="6fc64-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="6fc64-247">キーの暗号化 (`keyEncipherment`)</span><span class="sxs-lookup"><span data-stu-id="6fc64-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="6fc64-248">強化および拡張されたキーで使用</span><span class="sxs-lookup"><span data-stu-id="6fc64-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="6fc64-249">クライアント認証 (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="6fc64-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="6fc64-250">サーバー認証 (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="6fc64-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="6fc64-251">証明書を作成するには、次のいずれかの方法、または他の適切なツールやオンライン サービスを使用します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="6fc64-252">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6fc64-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="6fc64-253">Windows での MakeCert</span><span class="sxs-lookup"><span data-stu-id="6fc64-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="6fc64-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="6fc64-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="6fc64-255">パスワードを記録しておきます。これは後で Azure Key Vault に証明書をインポートするために使用します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="6fc64-256">Azure Key Vault 証明書の詳細については、[Azure Key Vault のCertificate\(次へ: 証明書\)](/azure/key-vault/certificates/) を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="6fc64-257">新しい Azure キー コンテナーを作成するか、Azure サブスクリプションの既存のキー コンテナーを使用します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="6fc64-258">キー コンテナーの **[証明書]** 領域で、PFX サイト証明書をインポートします。</span><span class="sxs-lookup"><span data-stu-id="6fc64-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="6fc64-259">後でアプリの構成に使用するので、証明書のサムプリントを記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="6fc64-260">Azure Key Vault で、:::no-loc(Identity)::: Server のトークン署名用に新しい自己署名証明書を生成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-260">In Azure Key Vault, generate a new self-signed certificate for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="6fc64-261">証明書の **証明書名** と **サブジェクト** を指定します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-261">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="6fc64-262">**サブジェクト** は `CN={COMMON NAME}` と指定します。`{COMMON NAME}` プレースホルダーは証明書の共通名です。</span><span class="sxs-lookup"><span data-stu-id="6fc64-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="6fc64-263">共通名には、任意の英数字を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="6fc64-264">たとえば、`CN=:::no-loc(Identity):::ServerSigning` は証明書の有効な **サブジェクト** です。</span><span class="sxs-lookup"><span data-stu-id="6fc64-264">For example, `CN=:::no-loc(Identity):::ServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="6fc64-265">**[ポリシーの詳細構成]** は既定の設定を使用します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="6fc64-266">後でアプリの構成に使用するので、証明書のサムプリントを記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="6fc64-267">Azure portal で Azure App Service に移動し、次の構成を使用して新しいアプリ サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="6fc64-268">**[発行]** は、`Code` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="6fc64-269">**[ランタイム スタック]** は、アプリのランタイムに設定します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="6fc64-270">**[SKU とサイズ]** については、App Service のレベルが `Basic B1` 以上であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-270">For **Sku and size** , confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="6fc64-271">App Service でカスタム ドメインを使用するには、`Basic B1` 以上のサービス レベルが必要です。</span><span class="sxs-lookup"><span data-stu-id="6fc64-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="6fc64-272">Azure によって App Service が作成された後、アプリの **[構成]** を開き、前に記録した証明書のサムプリントを指定して新しいアプリケーション設定を追加します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="6fc64-273">アプリ設定キーは `WEBSITE_LOAD_CERTIFICATES` です。</span><span class="sxs-lookup"><span data-stu-id="6fc64-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="6fc64-274">次の例に示すように、アプリの設定値で証明書のサムプリントを区切るにはコンマを使用します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="6fc64-275">キー: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="6fc64-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="6fc64-276">値: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="6fc64-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="6fc64-277">Azure portal で、アプリの設定の保存は 2 段階のプロセスです。`WEBSITE_LOAD_CERTIFICATES` のキーと値の設定を保存した後、ブレードの上部にある **[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="6fc64-278">アプリの **[TLS/SSL の設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-278">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="6fc64-279">**[秘密キー証明書 (.pfx)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-279">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="6fc64-280">**[Key Vault 証明書のインポート]** プロセスを 2 回使用して、HTTPS 通信用のサイトの証明書と、サイトの自己署名された :::no-loc(Identity)::: Server トークン署名証明書の両方をインポートします。</span><span class="sxs-lookup"><span data-stu-id="6fc64-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed :::no-loc(Identity)::: Server token signing certificate.</span></span>
1. <span data-ttu-id="6fc64-281">**[カスタム ドメイン]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="6fc64-282">ドメイン レジストラーの Web サイトで、 **IP アドレス** と **カスタム ドメイン検証 ID** を使用して、ドメインを構成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="6fc64-283">一般的なドメイン構成には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="6fc64-284">**ホスト** が `@` で、値が Azure portal の IP アドレスである **A レコード** 。</span><span class="sxs-lookup"><span data-stu-id="6fc64-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="6fc64-285">**ホスト** が `asuid` で、値が Azure によって生成されて Azure portal によって提供される検証 ID である **TXT レコード** 。</span><span class="sxs-lookup"><span data-stu-id="6fc64-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="6fc64-286">ドメイン レジストラーの Web サイトで変更を正しく保存したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="6fc64-287">レジストラーの Web サイトによっては、ドメイン レコードを保存するために 2 段階のプロセスが必要な場合があります。1 つ以上のレコードを個別に保存した後、別のボタンを使用してドメインの登録を更新します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="6fc64-288">Azure portal の **[カスタム ドメイン]** ブレードに戻ります。</span><span class="sxs-lookup"><span data-stu-id="6fc64-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="6fc64-289">**[カスタム ドメインの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-289">Select **Add custom domain**.</span></span> <span data-ttu-id="6fc64-290">**[A レコード]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-290">Select the **A Record** option.</span></span> <span data-ttu-id="6fc64-291">ドメインを指定し、 **[検証]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-291">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="6fc64-292">ドメイン レコードが正しく、インターネットを通して反映されている場合、ポータルで **[カスタム ドメインの追加]** ボタンを選択できます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="6fc64-293">ドメイン登録の変更がインターネットのドメイン ネーム サーバー (DNS) 全体に反映されるまで、ドメイン レジストラーによって処理されてから数日かかることがあります。</span><span class="sxs-lookup"><span data-stu-id="6fc64-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="6fc64-294">ドメイン レコードが 3 営業日以内に更新されない場合は、レコードが正しく設定されていることをドメイン レジストラーで確認し、カスタマー サポートに問い合わせてください。</span><span class="sxs-lookup"><span data-stu-id="6fc64-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="6fc64-295">**[カスタム ドメイン]** ブレードで、ドメインの **[SSL 状態]** は `Not Secure` とマークされます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="6fc64-296">**[バインドの追加]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-296">Select the **Add binding** link.</span></span> <span data-ttu-id="6fc64-297">キー コンテナーからカスタム ドメイン バインド用のサイトの HTTPS 証明書を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="6fc64-298">Visual Studio で、 *Server* プロジェクトのアプリ設定ファイル (`:::no-loc(appsettings.json):::` または `appsettings.Production.json`) を開きます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-298">In Visual Studio, open the *Server* project's app settings file (`:::no-loc(appsettings.json):::` or `appsettings.Production.json`).</span></span> <span data-ttu-id="6fc64-299">:::no-loc(Identity)::: Server の構成で、次の `Key` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-299">In the :::no-loc(Identity)::: Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="6fc64-300">`Name` キーに対しては、自己署名証明書の **サブジェクト** を指定します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="6fc64-301">次の例の場合、キー コンテナーで割り当てられている証明書の共通名は `:::no-loc(Identity):::ServerSigning` であり、それにより `CN=:::no-loc(Identity):::ServerSigning` という **サブジェクト** が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-301">In the following example, the certificate's common name assigned in the key vault is `:::no-loc(Identity):::ServerSigning`, which yields a **Subject** of `CN=:::no-loc(Identity):::ServerSigning`:</span></span>

   ```json
   ":::no-loc(Identity):::Server": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=:::no-loc(Identity):::ServerSigning"
     }
   },
   ```

1. <span data-ttu-id="6fc64-302">Visual Studio で、 *Server* プロジェクトに対する Azure App Service の " [発行プロファイル](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)" を作成します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="6fc64-303">メニュー バーから次のように選択します。 **[ビルド]**  >  **[発行]**  >  **[新規]**  >  **[Azure]**  >  **[Azure App Service]** (Windows または Linux)。</span><span class="sxs-lookup"><span data-stu-id="6fc64-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="6fc64-304">Visual Studio が Azure サブスクリプションに接続されたら、 **[リソースの種類]** で Azure リソースの **[ビュー]** を設定できます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="6fc64-305">**[Web アプリ]** の一覧内を移動し、アプリの App Service を見つけて選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="6fc64-306">**[完了]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-306">Select **Finish**.</span></span>
1. <span data-ttu-id="6fc64-307">Visual Studio が **[発行]** ウィンドウに戻ると、キー コンテナーと SQL Server データベース サービスの依存関係が自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="6fc64-308">Key Vault サービスの既定の設定で構成を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6fc64-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="6fc64-309">テストの目的で、既定では :::no-loc(Blazor)::: テンプレートによって構成されるアプリのローカル [SQLite](https://www.sqlite.org/index.html) データベースを、追加の構成を行わずにアプリでデプロイできます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the :::no-loc(Blazor)::: template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="6fc64-310">運用環境で :::no-loc(Identity)::: Server 用に別のデータベースを構成する方法については、この記事では説明しません。</span><span class="sxs-lookup"><span data-stu-id="6fc64-310">Configuring a different database for :::no-loc(Identity)::: Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="6fc64-311">詳細については、次のドキュメント セットのデータベース リソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6fc64-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="6fc64-312">App Service</span><span class="sxs-lookup"><span data-stu-id="6fc64-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="6fc64-313">:::no-loc(Identity)::: サーバー</span><span class="sxs-lookup"><span data-stu-id="6fc64-313">:::no-loc(Identity)::: Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="6fc64-314">ウィンドウの上部にある配置プロファイル名の下にある **[編集]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="6fc64-315">デプロイ先の URL を、サイトのカスタム ドメイン URL に変更します (例: `https://www.contoso.com`)。</span><span class="sxs-lookup"><span data-stu-id="6fc64-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="6fc64-316">設定を保存します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-316">Save the settings.</span></span>
1. <span data-ttu-id="6fc64-317">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="6fc64-317">Publish the app.</span></span> <span data-ttu-id="6fc64-318">Visual Studio によってブラウザー ウィンドウが開かれ、カスタム ドメインのサイトが要求されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="6fc64-319">Azure のドキュメントには、App Service の TLS バインドでの Azure サービスとカスタム ドメインの使用に関する詳細が含まれています。これには、A レコードの代わりに CNAME レコードを使用する方法の情報も含まれます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="6fc64-320">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6fc64-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="6fc64-321">App Service のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6fc64-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="6fc64-322">チュートリアル:既存のカスタム DNS 名を Azure App Service にマップする</span><span class="sxs-lookup"><span data-stu-id="6fc64-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="6fc64-323">Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="6fc64-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="6fc64-324">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6fc64-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="6fc64-325">Azure portal でアプリ、アプリの構成、または Azure サービスを変更した後は、アプリのテストを実行するたびに、新しいプライベートまたはシークレットのブラウザー ウィンドウを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6fc64-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="6fc64-326">サイトの構成が正しい場合でも、前回のテスト実行から残っている :::no-loc(cookie)::: により、サイトをテストするときに認証または承認が失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6fc64-326">Lingering :::no-loc(cookie):::s from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="6fc64-327">テストを実行するたびに新しいプライベートまたはシークレットのブラウザー ウィンドウを開くように Visual Studio を構成する方法の詳細については、「[:::no-loc(Cookie)::: とサイト データ](#:::no-loc(cookie):::s-and-site-data)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6fc64-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [:::no-loc(Cookie):::s and site data](#:::no-loc(cookie):::s-and-site-data) section.</span></span>

<span data-ttu-id="6fc64-328">Azure portal で App Service の構成を変更すると、通常、更新は短時間で有効になりますが、すぐにではありません。</span><span class="sxs-lookup"><span data-stu-id="6fc64-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="6fc64-329">場合によっては、構成の変更を有効にするために App Service が再起動されるまでしばらく待つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="6fc64-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="6fc64-330">証明書の読み込みに関する問題のトラブルシューティングを行う場合は、Azure portal の [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6fc64-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="6fc64-331">このコマンドにより、アプリで `My` > `CurrentUser` 証明書ストアからアクセスできる証明書の一覧が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6fc64-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="6fc64-332">出力には、アプリをデバッグするときに役立つ証明書のサブジェクトとサムプリントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6fc64-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6fc64-333">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6fc64-333">Additional resources</span></span>

* [<span data-ttu-id="6fc64-334">Azure App Service にデプロイする</span><span class="sxs-lookup"><span data-stu-id="6fc64-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="6fc64-335">Key Vault から証明書をインポートする (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="6fc64-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="6fc64-336">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="6fc64-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
