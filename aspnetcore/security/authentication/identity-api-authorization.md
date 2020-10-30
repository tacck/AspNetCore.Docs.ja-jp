---
title: ASP.NET Core でのシングルページアプリの認証の概要
author: javiercn
description: :::no-loc(Identity):::ASP.NET Core アプリ内でホストされるシングルページアプリで使用します。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 8acc34c88bf62b3da1b920acc7318c94435c100e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051981"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="9c874-103">SPAs の認証と承認</span><span class="sxs-lookup"><span data-stu-id="9c874-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="9c874-104">ASP.NET Core 3.1 以降のテンプレートでは、API 承認のサポートを使用して、シングルページアプリ (spa) で認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="9c874-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="9c874-105">:::no-loc(ASP.NET Core Identity):::認証と保存のために、ユーザーを[ :::no-loc(Identity)::: サーバー](https://identityserver.io/)と組み合わせて OpenID connect を実装します。</span><span class="sxs-lookup"><span data-stu-id="9c874-105">:::no-loc(ASP.NET Core Identity)::: for authenticating and storing users is combined with [:::no-loc(Identity):::Server](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="9c874-106">認証パラメーターが、 **Web アプリケーション (モデルビューコントローラー)** (MVC) および **web アプリケーション** (ページ) プロジェクトテンプレートの認証パラメーターに似た **角度** で、 **応答** するプロジェクトテンプレートに追加されました :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="9c874-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (:::no-loc(Razor)::: Pages) project templates.</span></span> <span data-ttu-id="9c874-107">許可されるパラメーター値は、 **None** および **個人** です。</span><span class="sxs-lookup"><span data-stu-id="9c874-107">The allowed parameter values are **None** and **Individual** .</span></span> <span data-ttu-id="9c874-108">**React.js と Redux** プロジェクトテンプレートでは、現時点では認証パラメーターがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9c874-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="9c874-109">API authorization サポートを使用してアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="9c874-109">Create an app with API authorization support</span></span>

<span data-ttu-id="9c874-110">ユーザーの認証と承認は、両方の角度で使用でき、SPAs として対応します。</span><span class="sxs-lookup"><span data-stu-id="9c874-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="9c874-111">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9c874-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="9c874-112">**角度** :</span><span class="sxs-lookup"><span data-stu-id="9c874-112">**Angular** :</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="9c874-113">**反応** :</span><span class="sxs-lookup"><span data-stu-id="9c874-113">**React** :</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="9c874-114">前のコマンドは、 *ClientApp* ディレクトリに SPA を含む ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9c874-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="9c874-115">アプリの ASP.NET Core コンポーネントの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="9c874-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="9c874-116">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="9c874-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="9c874-117">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="9c874-117">Startup class</span></span>

<span data-ttu-id="9c874-118">次のコード例は、AspNetCore に依存して [い :::no-loc(Identity)::: ます。サーバー](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="9c874-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server) NuGet package.</span></span> <span data-ttu-id="9c874-119">この例では、および拡張メソッドを使用して、API の認証と承認を構成し <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiResourceCollection.Add:::no-loc(Identity):::ServerJwt%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiResourceCollection.Add:::no-loc(Identity):::ServerJwt%2A> extension methods.</span></span> <span data-ttu-id="9c874-120">認証を使用して、応答または角速度の SPA プロジェクトテンプレートを使用するプロジェクトには、このパッケージへの参照が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9c874-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="9c874-121">クラスには `Startup` 、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="9c874-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="9c874-122">メソッド内 `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9c874-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="9c874-123">:::no-loc(Identity)::: 既定の UI では、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9c874-123">:::no-loc(Identity)::: with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefault:::no-loc(Identity):::<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="9c874-124">:::no-loc(Identity):::サーバーに追加のヘルパーメソッドを使用して、 `AddApiAuthorization` サーバー上に既定の ASP.NET Core 規則を設定する :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9c874-124">:::no-loc(Identity):::Server with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.Add:::no-loc(Identity):::Server()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="9c874-125">:::no-loc(Identity):::Server によって生成された JWT トークンを検証するようにアプリを構成する `Add:::no-loc(Identity):::ServerJwt` ヘルパー メソッドが追加された Authentication:</span><span class="sxs-lookup"><span data-stu-id="9c874-125">Authentication with an additional `Add:::no-loc(Identity):::ServerJwt` helper method that configures the app to validate JWT tokens produced by :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.AddAuthentication()
        .Add:::no-loc(Identity):::ServerJwt();
    ```

* <span data-ttu-id="9c874-126">メソッド内 `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9c874-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="9c874-127">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="9c874-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="9c874-128">:::no-loc(Identity):::OpenID connect エンドポイントを公開するサーバーミドルウェアは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9c874-128">The :::no-loc(Identity):::Server middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.Use:::no-loc(Identity):::Server();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="9c874-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="9c874-129">AddApiAuthorization</span></span>

<span data-ttu-id="9c874-130">このヘルパーメソッド :::no-loc(Identity)::: は、サポートされている構成を使用するようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="9c874-130">This helper method configures :::no-loc(Identity):::Server to use our supported configuration.</span></span> <span data-ttu-id="9c874-131">:::no-loc(Identity):::Server は、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="9c874-131">:::no-loc(Identity):::Server is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="9c874-132">同時に、最も一般的なシナリオでは不必要な複雑さを公開します。</span><span class="sxs-lookup"><span data-stu-id="9c874-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="9c874-133">そのため、適切な出発点と見なされる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9c874-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="9c874-134">認証を変更する必要がある場合でも、サーバーの能力を最大限に活用して、 :::no-loc(Identity)::: 必要に応じて認証をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="9c874-134">Once your authentication needs change, the full power of :::no-loc(Identity):::Server is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="9c874-135">Add:::no-loc(Identity):::ServerJwt</span><span class="sxs-lookup"><span data-stu-id="9c874-135">Add:::no-loc(Identity):::ServerJwt</span></span>

<span data-ttu-id="9c874-136">このヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="9c874-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="9c874-137">このポリシーは、 :::no-loc(Identity)::: :::no-loc(Identity)::: URL スペース "/" のサブパスにルーティングされるすべての要求を処理できるように構成されてい :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-137">The policy is configured to let :::no-loc(Identity)::: handle all requests routed to any subpath in the :::no-loc(Identity)::: URL space "/:::no-loc(Identity):::".</span></span> <span data-ttu-id="9c874-138">それ以外のすべての要求は、`JwtBearerHandler` で処理されます。</span><span class="sxs-lookup"><span data-stu-id="9c874-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="9c874-139">さらに、このメソッドは `<<ApplicationName>>API` API リソースを :::no-loc(Identity)::: の既定のスコープと共にサーバーに登録 `<<ApplicationName>>API` し、アプリケーションのサーバーによって発行されたトークンを検証するように JWT ベアラートークンミドルウェアを構成し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with :::no-loc(Identity):::Server with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by :::no-loc(Identity):::Server for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="9c874-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="9c874-140">WeatherForecastController</span></span>

<span data-ttu-id="9c874-141">*Controllers\WeatherForecastController.cs* ファイルで、属性がクラスに適用されていることを確認します。これは、 `[Authorize]` リソースにアクセスするための既定のポリシーに基づいてユーザーを承認する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="9c874-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="9c874-142">既定の承認ポリシーは、前に説明したポリシースキームによって設定される既定の認証スキームを使用するように構成され `Add:::no-loc(Identity):::ServerJwt` 、 `JwtBearerHandler` このようなヘルパーメソッドによって構成されたは、アプリに対する要求の既定のハンドラーになります。</span><span class="sxs-lookup"><span data-stu-id="9c874-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `Add:::no-loc(Identity):::ServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="9c874-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="9c874-143">ApplicationDbContext</span></span>

<span data-ttu-id="9c874-144">*Data\ApplicationDbContext.cs* ファイルで `DbContext` は、が拡張し :::no-loc(Identity)::: た例外 `ApiAuthorizationDbContext` (から派生したクラス) を使用して、 `:::no-loc(Identity):::DbContext` サーバーのスキーマを含めるという点に注意して :::no-loc(Identity)::: ください。</span><span class="sxs-lookup"><span data-stu-id="9c874-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in :::no-loc(Identity)::: with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `:::no-loc(Identity):::DbContext`) to include the schema for :::no-loc(Identity):::Server.</span></span>

<span data-ttu-id="9c874-145">データベーススキーマを完全に制御するには、使用可能なクラスの1つを継承 :::no-loc(Identity)::: `DbContext` し、メソッドでを呼び出してスキーマを含めるようにコンテキストを構成し :::no-loc(Identity)::: `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-145">To gain full control of the database schema, inherit from one of the available :::no-loc(Identity)::: `DbContext` classes and configure the context to include the :::no-loc(Identity)::: schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="9c874-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="9c874-146">OidcConfigurationController</span></span>

<span data-ttu-id="9c874-147">*Controllers\OidcConfigurationController.cs* ファイルで、クライアントが使用する必要のある oidc パラメーターを提供するためにプロビジョニングされたエンドポイントを確認します。</span><span class="sxs-lookup"><span data-stu-id="9c874-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### :::no-loc(appsettings.json):::

<span data-ttu-id="9c874-148">*:::no-loc(appsettings.json):::* プロジェクトルートのファイルには、 `:::no-loc(Identity):::Server` 構成されたクライアントの一覧を説明する新しいセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="9c874-148">In the *:::no-loc(appsettings.json):::* file of the project root, there's a new `:::no-loc(Identity):::Server` section that describes the list of configured clients.</span></span> <span data-ttu-id="9c874-149">次の例には、1 つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="9c874-149">In the following example, there's a single client.</span></span> <span data-ttu-id="9c874-150">クライアント名はアプリケーション名に対応し、規則によって OAuth の `ClientId` パラメーターにマップされます。</span><span class="sxs-lookup"><span data-stu-id="9c874-150">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="9c874-151">構成対象のアプリの種類は、プロファイルによって示されています。</span><span class="sxs-lookup"><span data-stu-id="9c874-151">The profile indicates the app type being configured.</span></span> <span data-ttu-id="9c874-152">サーバーの構成プロセスを簡略化する規則を実現するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9c874-152">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="9c874-153">「 [アプリケーションプロファイル](#application-profiles) 」セクションで説明されているように、使用可能なプロファイルがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="9c874-153">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": ":::no-loc(Identity):::ServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="9c874-154">appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="9c874-154">appsettings.Development.json</span></span>

<span data-ttu-id="9c874-155">プロジェクトルートの *appsettings.Development.js* のファイルには、 `:::no-loc(Identity):::Server` トークンの署名に使用されるキーについて説明するセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="9c874-155">In the *appsettings.Development.json* file of the project root, there's an `:::no-loc(Identity):::Server` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="9c874-156">運用環境にデプロイする場合は、「 [運用環境にデプロイする](#deploy-to-production) 」セクションで説明されているように、アプリと共にキーをプロビジョニングしてデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c874-156">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
":::no-loc(Identity):::Server": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="9c874-157">角度アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="9c874-157">General description of the Angular app</span></span>

<span data-ttu-id="9c874-158">角度テンプレートでの認証と API 承認のサポートは、独自の角度モジュールの *Clientapp-authorization* ディレクトリに存在します。</span><span class="sxs-lookup"><span data-stu-id="9c874-158">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="9c874-159">モジュールは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="9c874-159">The module is composed of the following elements:</span></span>

* <span data-ttu-id="9c874-160">3個のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="9c874-160">3 components:</span></span>
  * <span data-ttu-id="9c874-161">*login. component. ts* : アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="9c874-161">*login.component.ts* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="9c874-162">*logout* : アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="9c874-162">*logout.component.ts* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="9c874-163">*login-menu. component. ts* : 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="9c874-163">*login-menu.component.ts* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="9c874-164">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="9c874-164">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="9c874-165">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="9c874-165">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="9c874-166">ルートに追加できるルートガード `AuthorizeGuard` 。ルートにアクセスする前にユーザーを認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c874-166">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="9c874-167">`AuthorizeInterceptor`ユーザーが認証されるときに、API を対象とする発信 http 要求にアクセストークンを関連付ける http インターセプター。</span><span class="sxs-lookup"><span data-stu-id="9c874-167">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="9c874-168">`AuthorizeService`認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報を、使用するアプリの残りの部分に公開するサービス。</span><span class="sxs-lookup"><span data-stu-id="9c874-168">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="9c874-169">アプリの認証部分に関連付けられているルートを定義する角度モジュール。</span><span class="sxs-lookup"><span data-stu-id="9c874-169">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="9c874-170">ログインメニューコンポーネント、インターセプター、ガード、およびアプリの残りの部分から使用するためのサービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="9c874-170">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="9c874-171">反応アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="9c874-171">General description of the React app</span></span>

<span data-ttu-id="9c874-172">応答テンプレートでの認証と API 承認のサポートは、 *ClientApp\src\components\api-authorization* ディレクトリにあります。</span><span class="sxs-lookup"><span data-stu-id="9c874-172">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="9c874-173">これは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="9c874-173">It's composed of the following elements:</span></span>

* <span data-ttu-id="9c874-174">4つのコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="9c874-174">4 components:</span></span>
  * <span data-ttu-id="9c874-175">*Login.js* : アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="9c874-175">*Login.js* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="9c874-176">*Logout.js* : アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="9c874-176">*Logout.js* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="9c874-177">*LoginMenu.js* : 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="9c874-177">*LoginMenu.js* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="9c874-178">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="9c874-178">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="9c874-179">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="9c874-179">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="9c874-180">*AuthorizeRoute.js* : パラメーターに示されているコンポーネントを表示する前に、ユーザーを認証する必要があるルートコンポーネント。 `Component`</span><span class="sxs-lookup"><span data-stu-id="9c874-180">*AuthorizeRoute.js* : A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="9c874-181">`authService` `AuthorizeService` 認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開する、クラスのエクスポートされたインスタンス。</span><span class="sxs-lookup"><span data-stu-id="9c874-181">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="9c874-182">これで、ソリューションの主要なコンポーネントを確認できました。次は、アプリの個々のシナリオについて詳しく見ていきましょう。</span><span class="sxs-lookup"><span data-stu-id="9c874-182">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="9c874-183">新しい API での承認が必要</span><span class="sxs-lookup"><span data-stu-id="9c874-183">Require authorization on a new API</span></span>

<span data-ttu-id="9c874-184">既定では、システムは新しい Api の承認を要求するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9c874-184">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="9c874-185">これを行うには、新しいコントローラーを作成し、コントローラー `[Authorize]` クラスまたはコントローラー内の任意のアクションに属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="9c874-185">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="9c874-186">API 認証ハンドラーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="9c874-186">Customize the API authentication handler</span></span>

<span data-ttu-id="9c874-187">API の JWT ハンドラーの構成をカスタマイズするには、そのインスタンスを構成し <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-187">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .Add:::no-loc(Identity):::ServerJwt();

services.Configure<JwtBearerOptions>(
    :::no-loc(Identity):::ServerJwtConstants.:::no-loc(Identity):::ServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="9c874-188">API の JWT ハンドラーは、を使用して認証プロセスを制御できるようにするイベントを発生させ `JwtBearerEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-188">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="9c874-189">は、API 承認のサポートを提供するために、 `Add:::no-loc(Identity):::ServerJwt` 独自のイベントハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="9c874-189">To provide support for API authorization, `Add:::no-loc(Identity):::ServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="9c874-190">イベントの処理をカスタマイズするには、必要に応じて追加のロジックを使用して既存のイベントハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="9c874-190">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="9c874-191">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9c874-191">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    :::no-loc(Identity):::ServerJwtConstants.:::no-loc(Identity):::ServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="9c874-192">前のコードでは、 `OnTokenValidated` イベントハンドラーはカスタム実装に置き換えられています。</span><span class="sxs-lookup"><span data-stu-id="9c874-192">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="9c874-193">この実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9c874-193">This implementation:</span></span>

1. <span data-ttu-id="9c874-194">API 承認サポートによって提供される元の実装を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9c874-194">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="9c874-195">独自のカスタムロジックを実行します。</span><span class="sxs-lookup"><span data-stu-id="9c874-195">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="9c874-196">クライアント側のルートを保護する (角度)</span><span class="sxs-lookup"><span data-stu-id="9c874-196">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="9c874-197">クライアント側ルートの保護は、ルートを構成するときに実行するガードのリストに承認ガードを追加することによって行われます。</span><span class="sxs-lookup"><span data-stu-id="9c874-197">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="9c874-198">例として、主要なアプリの `fetch-data` 角度モジュール内でルートがどのように構成されているかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="9c874-198">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="9c874-199">ルートを保護しても実際のエンドポイントが保護されないことに注意してください (まだ属性が適用されている必要があり `[Authorize]` ます) が、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="9c874-199">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="9c874-200">API 要求の認証 (角度)</span><span class="sxs-lookup"><span data-stu-id="9c874-200">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="9c874-201">アプリと共にホストされる Api に対する要求の認証は、アプリによって定義された HTTP クライアントインターセプターを使用することによって自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="9c874-201">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="9c874-202">クライアント側のルートを保護する (応答)</span><span class="sxs-lookup"><span data-stu-id="9c874-202">Protect a client-side route (React)</span></span>

<span data-ttu-id="9c874-203">プレーンコンポーネントの代わりにコンポーネントを使用して、クライアント側のルートを保護し `AuthorizeRoute` `Route` ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-203">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="9c874-204">たとえば、 `fetch-data` コンポーネント内でルートがどのように構成されているかに注目して `App` ください。</span><span class="sxs-lookup"><span data-stu-id="9c874-204">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="9c874-205">ルートの保護:</span><span class="sxs-lookup"><span data-stu-id="9c874-205">Protecting a route:</span></span>

* <span data-ttu-id="9c874-206">は実際のエンドポイントを保護しません (まだ `[Authorize]` 属性が適用されている必要があります)。</span><span class="sxs-lookup"><span data-stu-id="9c874-206">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="9c874-207">は、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="9c874-207">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="9c874-208">API 要求の認証 (応答)</span><span class="sxs-lookup"><span data-stu-id="9c874-208">Authenticate API requests (React)</span></span>

<span data-ttu-id="9c874-209">を使用して要求を認証するには `authService` 、まずからインスタンスをインポートし `AuthorizeService` ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-209">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="9c874-210">アクセストークンはから取得され、 `authService` 次に示すように要求にアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="9c874-210">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="9c874-211">コンポーネントの反応では、通常、この作業は `componentDidMount` ライフサイクルメソッドで実行されるか、一部のユーザー操作の結果として行われます。</span><span class="sxs-lookup"><span data-stu-id="9c874-211">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="9c874-212">コンポーネントに authService をインポートする</span><span class="sxs-lookup"><span data-stu-id="9c874-212">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="9c874-213">アクセストークンを取得して応答にアタッチする</span><span class="sxs-lookup"><span data-stu-id="9c874-213">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="9c874-214">運用環境への配置</span><span class="sxs-lookup"><span data-stu-id="9c874-214">Deploy to production</span></span>

<span data-ttu-id="9c874-215">アプリを運用環境にデプロイするには、次のリソースをプロビジョニングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c874-215">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="9c874-216">:::no-loc(Identity):::ユーザーアカウントとサーバー権限を格納するデータベース :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9c874-216">A database to store the :::no-loc(Identity)::: user accounts and the :::no-loc(Identity):::Server grants.</span></span>
* <span data-ttu-id="9c874-217">トークンの署名に使用する実稼働証明書。</span><span class="sxs-lookup"><span data-stu-id="9c874-217">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="9c874-218">この証明書には特定の要件はありません。自己署名証明書、または CA 証明機関を通じてプロビジョニングされた証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9c874-218">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="9c874-219">PowerShell や OpenSSL などの標準ツールを使用して生成できます。</span><span class="sxs-lookup"><span data-stu-id="9c874-219">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="9c874-220">ターゲットコンピューターの証明書ストアにインストールすることも、強力なパスワードを使用して *.pfx* ファイルとして展開することもできます。</span><span class="sxs-lookup"><span data-stu-id="9c874-220">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="9c874-221">例: Azure 以外の web ホスティングプロバイダーへのデプロイ</span><span class="sxs-lookup"><span data-stu-id="9c874-221">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="9c874-222">Web ホスティングパネルで、証明書を作成または読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9c874-222">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="9c874-223">次に、アプリの *:::no-loc(appsettings.json):::* ファイルで、セクションを変更し `:::no-loc(Identity):::Server` てキーの詳細を含めます。</span><span class="sxs-lookup"><span data-stu-id="9c874-223">Then in the app's *:::no-loc(appsettings.json):::* file, modify the `:::no-loc(Identity):::Server` section to include the key details.</span></span> <span data-ttu-id="9c874-224">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9c874-224">For example:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="9c874-225">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="9c874-225">In the preceding example:</span></span>

* <span data-ttu-id="9c874-226">`StoreName` 証明書が格納されている証明書ストアの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="9c874-226">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="9c874-227">この例では、web ホスティングストアを参照しています。</span><span class="sxs-lookup"><span data-stu-id="9c874-227">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="9c874-228">`StoreLocation` 証明書の読み込み元 ( `CurrentUser` この場合は) を表します。</span><span class="sxs-lookup"><span data-stu-id="9c874-228">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="9c874-229">`Name` 証明書の識別サブジェクトに対応します。</span><span class="sxs-lookup"><span data-stu-id="9c874-229">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="9c874-230">例: Azure App Service に配置する</span><span class="sxs-lookup"><span data-stu-id="9c874-230">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="9c874-231">このセクションでは、証明書ストアに格納されている証明書を使用して Azure App Service するためのアプリの展開について説明します。</span><span class="sxs-lookup"><span data-stu-id="9c874-231">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="9c874-232">証明書ストアから証明書を読み込むようにアプリを変更するには、後の手順で Azure portal でアプリを構成するときに、Standard レベルのサービスプラン以上が必要です。</span><span class="sxs-lookup"><span data-stu-id="9c874-232">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="9c874-233">アプリのファイルで *:::no-loc(appsettings.json):::* 、セクションを変更し `:::no-loc(Identity):::Server` て、キーの詳細を含めます。</span><span class="sxs-lookup"><span data-stu-id="9c874-233">In the app's *:::no-loc(appsettings.json):::* file, modify the `:::no-loc(Identity):::Server` section to include the key details:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="9c874-234">ストア名は、証明書が格納されている証明書ストアの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="9c874-234">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="9c874-235">この場合、個人ユーザーストアを指します。</span><span class="sxs-lookup"><span data-stu-id="9c874-235">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="9c874-236">ストアの場所は、証明書を読み込む場所 ( `CurrentUser` または) を表し `LocalMachine` ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-236">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="9c874-237">Certificate の name プロパティは、証明書の識別サブジェクトに対応します。</span><span class="sxs-lookup"><span data-stu-id="9c874-237">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="9c874-238">Azure App Service にデプロイするには、「 [azure へのアプリのデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)」の手順に従って、必要な azure リソースを作成し、アプリを運用環境にデプロイする方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="9c874-238">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="9c874-239">前述の手順に従うと、アプリは Azure にデプロイされますが、まだ機能していません。</span><span class="sxs-lookup"><span data-stu-id="9c874-239">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="9c874-240">アプリによって使用される証明書は、Azure portal で構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c874-240">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="9c874-241">証明書のサムプリントを見つけて、「 [証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)」で説明されている手順に従います。</span><span class="sxs-lookup"><span data-stu-id="9c874-241">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="9c874-242">これらの手順では SSL について説明していますが、Azure portal には、アプリで使用するプロビジョニング済みの証明書をアップロードできる **プライベート証明書** セクションがあります。</span><span class="sxs-lookup"><span data-stu-id="9c874-242">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="9c874-243">Azure portal でアプリとアプリの設定を構成した後、ポータルでアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="9c874-243">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="9c874-244">その他の構成オプション</span><span class="sxs-lookup"><span data-stu-id="9c874-244">Other configuration options</span></span>

<span data-ttu-id="9c874-245">API 承認のサポートは、 :::no-loc(Identity)::: 一連の規則、既定値、および拡張機能を使用してサーバー上に構築され、SPAs のエクスペリエンスが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="9c874-245">The support for API authorization builds on top of :::no-loc(Identity):::Server with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="9c874-246">言うまで :::no-loc(Identity)::: もありませんが、ASP.NET Core 統合によってシナリオがカバーされていない場合は、サーバーの全機能をバックグラウンドで利用できます。</span><span class="sxs-lookup"><span data-stu-id="9c874-246">Needless to say, the full power of :::no-loc(Identity):::Server is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="9c874-247">ASP.NET Core サポートは、すべてのアプリが組織によって作成および展開される "ファーストパーティ" アプリに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="9c874-247">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="9c874-248">そのため、同意やフェデレーションなどのサポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="9c874-248">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="9c874-249">これらのシナリオでは、Server を使用 :::no-loc(Identity)::: して、そのドキュメントに従ってください。</span><span class="sxs-lookup"><span data-stu-id="9c874-249">For those scenarios, use :::no-loc(Identity):::Server and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="9c874-250">アプリケーションプロファイル</span><span class="sxs-lookup"><span data-stu-id="9c874-250">Application profiles</span></span>

<span data-ttu-id="9c874-251">アプリケーションプロファイルは、そのパラメーターをさらに定義するアプリの事前定義された構成です。</span><span class="sxs-lookup"><span data-stu-id="9c874-251">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="9c874-252">現時点では、次のプロファイルがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9c874-252">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="9c874-253">`:::no-loc(Identity):::ServerSPA`: サーバーと共にホストされる SPA を :::no-loc(Identity)::: 1 つの単位として表します。</span><span class="sxs-lookup"><span data-stu-id="9c874-253">`:::no-loc(Identity):::ServerSPA`: Represents a SPA hosted alongside :::no-loc(Identity):::Server as a single unit.</span></span>
  * <span data-ttu-id="9c874-254">の `redirect_uri` 既定値は `/authentication/login-callback` です。</span><span class="sxs-lookup"><span data-stu-id="9c874-254">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="9c874-255">の `post_logout_redirect_uri` 既定値は `/authentication/logout-callback` です。</span><span class="sxs-lookup"><span data-stu-id="9c874-255">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="9c874-256">スコープのセットには、 `openid` `profile` アプリ内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9c874-256">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="9c874-257">許可される OIDC 応答の種類のセットは、 `id_token token` それぞれ個別に ( `id_token` 、 `token` ) です。</span><span class="sxs-lookup"><span data-stu-id="9c874-257">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="9c874-258">許可される応答モードは `fragment` です。</span><span class="sxs-lookup"><span data-stu-id="9c874-258">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="9c874-259">`SPA`: サーバーでホストされていない SPA を表し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-259">`SPA`: Represents a SPA that isn't hosted with :::no-loc(Identity):::Server.</span></span>
  * <span data-ttu-id="9c874-260">スコープのセットには、 `openid` `profile` アプリ内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9c874-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="9c874-261">許可される OIDC 応答の種類のセットは、 `id_token token` それぞれ個別に ( `id_token` 、 `token` ) です。</span><span class="sxs-lookup"><span data-stu-id="9c874-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="9c874-262">許可される応答モードは `fragment` です。</span><span class="sxs-lookup"><span data-stu-id="9c874-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="9c874-263">`:::no-loc(Identity):::ServerJwt`: サーバーと共にホストされる API を表し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-263">`:::no-loc(Identity):::ServerJwt`: Represents an API that is hosted alongside with :::no-loc(Identity):::Server.</span></span>
  * <span data-ttu-id="9c874-264">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9c874-264">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="9c874-265">`API`: サーバーでホストされていない API を表し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-265">`API`: Represents an API that isn't hosted with :::no-loc(Identity):::Server.</span></span>
  * <span data-ttu-id="9c874-266">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9c874-266">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="9c874-267">AppSettings を使用した構成</span><span class="sxs-lookup"><span data-stu-id="9c874-267">Configuration through AppSettings</span></span>

<span data-ttu-id="9c874-268">構成システムを使用して、またはの一覧にアプリを追加して、アプリを構成し `Clients` `Resources` ます。</span><span class="sxs-lookup"><span data-stu-id="9c874-268">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="9c874-269">`redirect_uri` `post_logout_redirect_uri` 次の例に示すように、各クライアントのおよびプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="9c874-269">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="9c874-270">リソースを構成するときは、次に示すように、リソースのスコープを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9c874-270">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="9c874-271">コードを使用した構成</span><span class="sxs-lookup"><span data-stu-id="9c874-271">Configuration through code</span></span>

<span data-ttu-id="9c874-272">また、 `AddApiAuthorization` オプションを構成するためのアクションを実行するのオーバーロードを使用して、コードを使用してクライアントとリソースを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="9c874-272">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="9c874-273">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="9c874-273">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
