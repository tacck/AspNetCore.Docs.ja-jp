---
title: ASP.NET Core でのシングルページアプリの認証の概要
author: javiercn
description: IdentityASP.NET Core アプリ内でホストされるシングルページアプリで使用します。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 2b587517268208dcf66cd2895b7aa22bfa381f84
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060359"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="be89d-103">SPAs の認証と承認</span><span class="sxs-lookup"><span data-stu-id="be89d-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="be89d-104">ASP.NET Core 3.0 以降では、API 承認のサポートを使用して、シングルページアプリ (spa) で認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="be89d-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="be89d-105">Identityユーザーを認証および格納するための ASP.NET Core は、OPEN ID Connect を実装するため[に、](https://identityserver.io/)ユーザーと組み合わせて使用されます。</span><span class="sxs-lookup"><span data-stu-id="be89d-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="be89d-106">認証パラメーターが、 **Web アプリケーション (モデルビューコントローラー)** (MVC) および**web アプリケーション**(ページ) プロジェクトテンプレートの認証パラメーターに似た**角度**で、**応答**するプロジェクトテンプレートに追加されました Razor 。</span><span class="sxs-lookup"><span data-stu-id="be89d-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="be89d-107">許可されるパラメーター値は、 **None**および**個人**です。</span><span class="sxs-lookup"><span data-stu-id="be89d-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="be89d-108">**React.js と Redux**プロジェクトテンプレートでは、現時点では認証パラメーターがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="be89d-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="be89d-109">API authorization サポートを使用してアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="be89d-109">Create an app with API authorization support</span></span>

<span data-ttu-id="be89d-110">ユーザーの認証と承認は、両方の角度で使用でき、SPAs として対応します。</span><span class="sxs-lookup"><span data-stu-id="be89d-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="be89d-111">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="be89d-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="be89d-112">**角度**:</span><span class="sxs-lookup"><span data-stu-id="be89d-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="be89d-113">**反応**:</span><span class="sxs-lookup"><span data-stu-id="be89d-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="be89d-114">前のコマンドは、 *ClientApp*ディレクトリに SPA を含む ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="be89d-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="be89d-115">アプリの ASP.NET Core コンポーネントの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="be89d-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="be89d-116">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="be89d-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="be89d-117">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="be89d-117">Startup class</span></span>

<span data-ttu-id="be89d-118">次のコード例は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet パッケージに依存しています。</span><span class="sxs-lookup"><span data-stu-id="be89d-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="be89d-119">この例では、および拡張メソッドを使用して、API の認証と承認を構成し <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="be89d-120">認証を使用して、応答または角速度の SPA プロジェクトテンプレートを使用するプロジェクトには、このパッケージへの参照が含まれます。</span><span class="sxs-lookup"><span data-stu-id="be89d-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="be89d-121">クラスには `Startup` 、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="be89d-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="be89d-122">メソッド内 `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="be89d-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * Identity<span data-ttu-id="be89d-123">既定の UI では、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="be89d-123"> with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="be89d-124">サーバーには、次のように、 `AddApiAuthorization` 既定の ASP.NET Core 規則を設定する追加のヘルパーメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="be89d-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="be89d-125">IdentityServer によって生成された JWT トークンを検証するようにアプリを構成する `AddIdentityServerJwt` ヘルパー メソッドが追加された Authentication。</span><span class="sxs-lookup"><span data-stu-id="be89d-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="be89d-126">メソッド内 `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="be89d-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="be89d-127">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="be89d-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="be89d-128">Open ID Connect エンドポイントを公開するサーバーミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="be89d-128">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="be89d-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="be89d-129">AddApiAuthorization</span></span>

<span data-ttu-id="be89d-130">このヘルパーメソッドは、サポートされる構成を使用するようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="be89d-130">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="be89d-131">IdentityServer は、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="be89d-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="be89d-132">同時に、最も一般的なシナリオでは不必要な複雑さを公開します。</span><span class="sxs-lookup"><span data-stu-id="be89d-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="be89d-133">そのため、適切な出発点と見なされる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="be89d-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="be89d-134">認証を変更する必要がある場合でも、ユーザーのニーズに合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="be89d-134">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="be89d-135">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="be89d-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="be89d-136">このヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="be89d-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="be89d-137">このポリシーは、 Identity Identity URL スペース "/" のサブパスにルーティングされるすべての要求を処理できるように構成されてい Identity ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-137">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="be89d-138">それ以外のすべての要求は、`JwtBearerHandler` で処理されます。</span><span class="sxs-lookup"><span data-stu-id="be89d-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="be89d-139">さらに、このメソッドは `<<ApplicationName>>API` API リソースをの既定のスコープに登録 `<<ApplicationName>>API` し、JWT ベアラートークンミドルウェアを構成して、アプリのために、サービスによって発行されたトークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="be89d-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="be89d-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="be89d-140">WeatherForecastController</span></span>

<span data-ttu-id="be89d-141">*Controllers\WeatherForecastController.cs*ファイルで、属性がクラスに適用されていることを確認します。これは、 `[Authorize]` リソースにアクセスするための既定のポリシーに基づいてユーザーを承認する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="be89d-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="be89d-142">既定の承認ポリシーは、前に説明したポリシースキームによって設定される既定の認証スキームを使用するように構成され `AddIdentityServerJwt` 、 `JwtBearerHandler` このようなヘルパーメソッドによって構成されたは、アプリに対する要求の既定のハンドラーになります。</span><span class="sxs-lookup"><span data-stu-id="be89d-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="be89d-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="be89d-143">ApplicationDbContext</span></span>

<span data-ttu-id="be89d-144">*Data\ApplicationDbContext.cs*ファイルで `DbContext` は、が Identity 拡張する例外 `ApiAuthorizationDbContext` (から派生したクラス) を使用して、で同じを使用し `IdentityDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="be89d-145">データベーススキーマを完全に制御するには、使用可能なクラスの1つを継承 Identity `DbContext` し、メソッドでを呼び出してスキーマを含めるようにコンテキストを構成し Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-145">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="be89d-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="be89d-146">OidcConfigurationController</span></span>

<span data-ttu-id="be89d-147">*Controllers\OidcConfigurationController.cs*ファイルで、クライアントが使用する必要のある oidc パラメーターを提供するためにプロビジョニングされたエンドポイントを確認します。</span><span class="sxs-lookup"><span data-stu-id="be89d-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="be89d-148">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="be89d-148">appsettings.json</span></span>

<span data-ttu-id="be89d-149">プロジェクトルートの*appsettings.js*のファイルには、 `IdentityServer` 構成されたクライアントの一覧を説明する新しいセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="be89d-149">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="be89d-150">次の例には、1 つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="be89d-150">In the following example, there's a single client.</span></span> <span data-ttu-id="be89d-151">クライアント名はアプリケーション名に対応し、規則によって OAuth の `ClientId` パラメーターにマップされます。</span><span class="sxs-lookup"><span data-stu-id="be89d-151">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="be89d-152">構成対象のアプリの種類は、プロファイルによって示されています。</span><span class="sxs-lookup"><span data-stu-id="be89d-152">The profile indicates the app type being configured.</span></span> <span data-ttu-id="be89d-153">サーバーの構成プロセスを簡略化する規則を実現するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="be89d-153">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="be89d-154">「[アプリケーションプロファイル](#application-profiles)」セクションで説明されているように、使用可能なプロファイルがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="be89d-154">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="be89d-155">appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="be89d-155">appsettings.Development.json</span></span>

<span data-ttu-id="be89d-156">プロジェクトルートの*appsettings.Development.js*のファイルには、 `IdentityServer` トークンの署名に使用されるキーについて説明するセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="be89d-156">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="be89d-157">運用環境にデプロイする場合は、「[運用環境にデプロイする](#deploy-to-production)」セクションで説明されているように、アプリと共にキーをプロビジョニングしてデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="be89d-157">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="be89d-158">角度アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="be89d-158">General description of the Angular app</span></span>

<span data-ttu-id="be89d-159">角度テンプレートでの認証と API 承認のサポートは、独自の角度モジュールの*Clientapp-authorization*ディレクトリに存在します。</span><span class="sxs-lookup"><span data-stu-id="be89d-159">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="be89d-160">モジュールは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="be89d-160">The module is composed of the following elements:</span></span>

* <span data-ttu-id="be89d-161">3個のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="be89d-161">3 components:</span></span>
  * <span data-ttu-id="be89d-162">*login. component. ts*: アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="be89d-162">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="be89d-163">*logout*: アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="be89d-163">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="be89d-164">*login-menu. component. ts*: 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="be89d-164">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="be89d-165">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="be89d-165">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="be89d-166">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="be89d-166">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="be89d-167">ルートに追加できるルートガード `AuthorizeGuard` 。ルートにアクセスする前にユーザーを認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="be89d-167">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="be89d-168">`AuthorizeInterceptor`ユーザーが認証されるときに、API を対象とする発信 http 要求にアクセストークンを関連付ける http インターセプター。</span><span class="sxs-lookup"><span data-stu-id="be89d-168">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="be89d-169">`AuthorizeService`認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報を、使用するアプリの残りの部分に公開するサービス。</span><span class="sxs-lookup"><span data-stu-id="be89d-169">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="be89d-170">アプリの認証部分に関連付けられているルートを定義する角度モジュール。</span><span class="sxs-lookup"><span data-stu-id="be89d-170">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="be89d-171">ログインメニューコンポーネント、インターセプター、ガード、およびアプリの残りの部分から使用するためのサービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="be89d-171">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="be89d-172">反応アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="be89d-172">General description of the React app</span></span>

<span data-ttu-id="be89d-173">応答テンプレートでの認証と API 承認のサポートは、 *ClientApp\src\components\api-authorization*ディレクトリにあります。</span><span class="sxs-lookup"><span data-stu-id="be89d-173">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="be89d-174">これは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="be89d-174">It's composed of the following elements:</span></span>

* <span data-ttu-id="be89d-175">4つのコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="be89d-175">4 components:</span></span>
  * <span data-ttu-id="be89d-176">*Login.js*: アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="be89d-176">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="be89d-177">*Logout.js*: アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="be89d-177">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="be89d-178">*LoginMenu.js*: 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="be89d-178">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="be89d-179">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="be89d-179">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="be89d-180">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="be89d-180">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="be89d-181">*AuthorizeRoute.js*: パラメーターに示されているコンポーネントを表示する前に、ユーザーを認証する必要があるルートコンポーネント。 `Component`</span><span class="sxs-lookup"><span data-stu-id="be89d-181">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="be89d-182">`authService` `AuthorizeService` 認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開する、クラスのエクスポートされたインスタンス。</span><span class="sxs-lookup"><span data-stu-id="be89d-182">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="be89d-183">これで、ソリューションの主要なコンポーネントを確認できました。次は、アプリの個々のシナリオについて詳しく見ていきましょう。</span><span class="sxs-lookup"><span data-stu-id="be89d-183">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="be89d-184">新しい API での承認が必要</span><span class="sxs-lookup"><span data-stu-id="be89d-184">Require authorization on a new API</span></span>

<span data-ttu-id="be89d-185">既定では、システムは新しい Api の承認を要求するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="be89d-185">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="be89d-186">これを行うには、新しいコントローラーを作成し、コントローラー `[Authorize]` クラスまたはコントローラー内の任意のアクションに属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="be89d-186">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="be89d-187">API 認証ハンドラーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="be89d-187">Customize the API authentication handler</span></span>

<span data-ttu-id="be89d-188">API の JWT ハンドラーの構成をカスタマイズするには、そのインスタンスを構成し <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-188">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="be89d-189">API の JWT ハンドラーは、を使用して認証プロセスを制御できるようにするイベントを発生させ `JwtBearerEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-189">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="be89d-190">は、API 承認のサポートを提供するために、 `AddIdentityServerJwt` 独自のイベントハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="be89d-190">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="be89d-191">イベントの処理をカスタマイズするには、必要に応じて追加のロジックを使用して既存のイベントハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="be89d-191">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="be89d-192">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="be89d-192">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
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

<span data-ttu-id="be89d-193">前のコードでは、 `OnTokenValidated` イベントハンドラーはカスタム実装に置き換えられています。</span><span class="sxs-lookup"><span data-stu-id="be89d-193">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="be89d-194">この実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="be89d-194">This implementation:</span></span>

1. <span data-ttu-id="be89d-195">API 承認サポートによって提供される元の実装を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="be89d-195">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="be89d-196">独自のカスタムロジックを実行します。</span><span class="sxs-lookup"><span data-stu-id="be89d-196">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="be89d-197">クライアント側のルートを保護する (角度)</span><span class="sxs-lookup"><span data-stu-id="be89d-197">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="be89d-198">クライアント側ルートの保護は、ルートを構成するときに実行するガードのリストに承認ガードを追加することによって行われます。</span><span class="sxs-lookup"><span data-stu-id="be89d-198">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="be89d-199">例として、主要なアプリの `fetch-data` 角度モジュール内でルートがどのように構成されているかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="be89d-199">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="be89d-200">ルートを保護しても実際のエンドポイントが保護されないことに注意してください (まだ属性が適用されている必要があり `[Authorize]` ます) が、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="be89d-200">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="be89d-201">API 要求の認証 (角度)</span><span class="sxs-lookup"><span data-stu-id="be89d-201">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="be89d-202">アプリと共にホストされる Api に対する要求の認証は、アプリによって定義された HTTP クライアントインターセプターを使用することによって自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="be89d-202">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="be89d-203">クライアント側のルートを保護する (応答)</span><span class="sxs-lookup"><span data-stu-id="be89d-203">Protect a client-side route (React)</span></span>

<span data-ttu-id="be89d-204">プレーンコンポーネントの代わりにコンポーネントを使用して、クライアント側のルートを保護し `AuthorizeRoute` `Route` ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-204">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="be89d-205">たとえば、 `fetch-data` コンポーネント内でルートがどのように構成されているかに注目して `App` ください。</span><span class="sxs-lookup"><span data-stu-id="be89d-205">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="be89d-206">ルートの保護:</span><span class="sxs-lookup"><span data-stu-id="be89d-206">Protecting a route:</span></span>

* <span data-ttu-id="be89d-207">は実際のエンドポイントを保護しません (まだ `[Authorize]` 属性が適用されている必要があります)。</span><span class="sxs-lookup"><span data-stu-id="be89d-207">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="be89d-208">は、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="be89d-208">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="be89d-209">API 要求の認証 (応答)</span><span class="sxs-lookup"><span data-stu-id="be89d-209">Authenticate API requests (React)</span></span>

<span data-ttu-id="be89d-210">を使用して要求を認証するには `authService` 、まずからインスタンスをインポートし `AuthorizeService` ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-210">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="be89d-211">アクセストークンはから取得され、 `authService` 次に示すように要求にアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="be89d-211">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="be89d-212">コンポーネントの反応では、通常、この作業は `componentDidMount` ライフサイクルメソッドで実行されるか、一部のユーザー操作の結果として行われます。</span><span class="sxs-lookup"><span data-stu-id="be89d-212">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="be89d-213">コンポーネントに authService をインポートする</span><span class="sxs-lookup"><span data-stu-id="be89d-213">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="be89d-214">アクセストークンを取得して応答にアタッチする</span><span class="sxs-lookup"><span data-stu-id="be89d-214">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="be89d-215">運用環境への配置</span><span class="sxs-lookup"><span data-stu-id="be89d-215">Deploy to production</span></span>

<span data-ttu-id="be89d-216">アプリを運用環境にデプロイするには、次のリソースをプロビジョニングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="be89d-216">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="be89d-217">Identityユーザーアカウントとサーバー権限を格納するデータベース。</span><span class="sxs-lookup"><span data-stu-id="be89d-217">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="be89d-218">トークンの署名に使用する実稼働証明書。</span><span class="sxs-lookup"><span data-stu-id="be89d-218">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="be89d-219">この証明書には特定の要件はありません。自己署名証明書、または CA 証明機関を通じてプロビジョニングされた証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="be89d-219">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="be89d-220">PowerShell や OpenSSL などの標準ツールを使用して生成できます。</span><span class="sxs-lookup"><span data-stu-id="be89d-220">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="be89d-221">ターゲットコンピューターの証明書ストアにインストールすることも、強力なパスワードを使用して *.pfx*ファイルとして展開することもできます。</span><span class="sxs-lookup"><span data-stu-id="be89d-221">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="be89d-222">例: Azure App Service に配置する</span><span class="sxs-lookup"><span data-stu-id="be89d-222">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="be89d-223">このセクションでは、証明書ストアに格納されている証明書を使用して Azure App Service するためのアプリの展開について説明します。</span><span class="sxs-lookup"><span data-stu-id="be89d-223">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="be89d-224">証明書ストアから証明書を読み込むようにアプリを変更するには、後の手順で Azure portal でアプリを構成するときに、Standard レベルのサービスプラン以上が必要です。</span><span class="sxs-lookup"><span data-stu-id="be89d-224">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="be89d-225">アプリの*appsettings.js*ファイルで、セクションを変更して、 `IdentityServer` キーの詳細を含めます。</span><span class="sxs-lookup"><span data-stu-id="be89d-225">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="be89d-226">ストア名は、証明書が格納されている証明書ストアの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="be89d-226">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="be89d-227">この場合、個人ユーザーストアを指します。</span><span class="sxs-lookup"><span data-stu-id="be89d-227">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="be89d-228">ストアの場所は、証明書を読み込む場所 ( `CurrentUser` または) を表し `LocalMachine` ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-228">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="be89d-229">Certificate の name プロパティは、証明書の識別サブジェクトに対応します。</span><span class="sxs-lookup"><span data-stu-id="be89d-229">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="be89d-230">Azure App Service にデプロイするには、「 [azure へのアプリのデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)」の手順に従って、必要な azure リソースを作成し、アプリを運用環境にデプロイする方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="be89d-230">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="be89d-231">前述の手順に従うと、アプリは Azure にデプロイされますが、まだ機能していません。</span><span class="sxs-lookup"><span data-stu-id="be89d-231">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="be89d-232">アプリによって使用される証明書は、Azure portal で構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="be89d-232">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="be89d-233">証明書のサムプリントを見つけて、「[証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)」で説明されている手順に従います。</span><span class="sxs-lookup"><span data-stu-id="be89d-233">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="be89d-234">これらの手順では SSL について説明していますが、Azure portal には、アプリで使用するプロビジョニング済みの証明書をアップロードできる**プライベート証明書**セクションがあります。</span><span class="sxs-lookup"><span data-stu-id="be89d-234">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="be89d-235">Azure portal でアプリとアプリの設定を構成した後、ポータルでアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="be89d-235">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="be89d-236">その他の構成オプション</span><span class="sxs-lookup"><span data-stu-id="be89d-236">Other configuration options</span></span>

<span data-ttu-id="be89d-237">API 承認のサポートは、一連の規則、既定値、および拡張機能を使用して、サーバー上に構築されます。これにより、SPAs のエクスペリエンスが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="be89d-237">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="be89d-238">言うまでもありませんが、ASP.NET Core 統合によって実際のシナリオがカバーされていない場合は、サーバーの全機能をバックグラウンドで利用できます。</span><span class="sxs-lookup"><span data-stu-id="be89d-238">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="be89d-239">ASP.NET Core サポートは、すべてのアプリが組織によって作成および展開される "ファーストパーティ" アプリに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="be89d-239">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="be89d-240">そのため、同意やフェデレーションなどのサポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="be89d-240">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="be89d-241">これらのシナリオでは、ユーザーを使用して、そのドキュメントに従ってください。</span><span class="sxs-lookup"><span data-stu-id="be89d-241">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="be89d-242">アプリケーションプロファイル</span><span class="sxs-lookup"><span data-stu-id="be89d-242">Application profiles</span></span>

<span data-ttu-id="be89d-243">アプリケーションプロファイルは、そのパラメーターをさらに定義するアプリの事前定義された構成です。</span><span class="sxs-lookup"><span data-stu-id="be89d-243">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="be89d-244">現時点では、次のプロファイルがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="be89d-244">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="be89d-245">`IdentityServerSPA`: サーバーと共にホストされる SPA を1つの単位として表します。</span><span class="sxs-lookup"><span data-stu-id="be89d-245">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="be89d-246">の `redirect_uri` 既定値は `/authentication/login-callback` です。</span><span class="sxs-lookup"><span data-stu-id="be89d-246">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="be89d-247">の `post_logout_redirect_uri` 既定値は `/authentication/logout-callback` です。</span><span class="sxs-lookup"><span data-stu-id="be89d-247">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="be89d-248">スコープのセットには、 `openid` `profile` アプリ内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="be89d-248">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="be89d-249">許可される OIDC 応答の種類のセットは、 `id_token token` それぞれ個別に ( `id_token` 、 `token` ) です。</span><span class="sxs-lookup"><span data-stu-id="be89d-249">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="be89d-250">許可される応答モードは `fragment` です。</span><span class="sxs-lookup"><span data-stu-id="be89d-250">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="be89d-251">`SPA`: は、サーバーでホストされていない SPA を表します。</span><span class="sxs-lookup"><span data-stu-id="be89d-251">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="be89d-252">スコープのセットには、 `openid` `profile` アプリ内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="be89d-252">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="be89d-253">許可される OIDC 応答の種類のセットは、 `id_token token` それぞれ個別に ( `id_token` 、 `token` ) です。</span><span class="sxs-lookup"><span data-stu-id="be89d-253">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="be89d-254">許可される応答モードは `fragment` です。</span><span class="sxs-lookup"><span data-stu-id="be89d-254">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="be89d-255">`IdentityServerJwt`: サービスと共にホストされる API を表します。</span><span class="sxs-lookup"><span data-stu-id="be89d-255">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="be89d-256">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="be89d-256">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="be89d-257">`API`: は、サーバーでホストされていない API を表します。</span><span class="sxs-lookup"><span data-stu-id="be89d-257">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="be89d-258">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="be89d-258">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="be89d-259">AppSettings を使用した構成</span><span class="sxs-lookup"><span data-stu-id="be89d-259">Configuration through AppSettings</span></span>

<span data-ttu-id="be89d-260">構成システムを使用して、またはの一覧にアプリを追加して、アプリを構成し `Clients` `Resources` ます。</span><span class="sxs-lookup"><span data-stu-id="be89d-260">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="be89d-261">`redirect_uri` `post_logout_redirect_uri` 次の例に示すように、各クライアントのおよびプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="be89d-261">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="be89d-262">リソースを構成するときは、次に示すように、リソースのスコープを構成できます。</span><span class="sxs-lookup"><span data-stu-id="be89d-262">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="be89d-263">コードを使用した構成</span><span class="sxs-lookup"><span data-stu-id="be89d-263">Configuration through code</span></span>

<span data-ttu-id="be89d-264">また、 `AddApiAuthorization` オプションを構成するためのアクションを実行するのオーバーロードを使用して、コードを使用してクライアントとリソースを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="be89d-264">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="be89d-265">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="be89d-265">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
