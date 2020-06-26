---
title: 認証と Identity ASP.NET Core 2.0 への移行
author: scottaddie
description: この記事では ASP.NET Core 1.x 認証と ASP.NET Core 2.0 に移行するための最も一般的な手順について説明し Identity ます。
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: dacf6fa7191f51f36b9ba65a90746a26f958fc03
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408670"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="c14ea-103">認証と Identity ASP.NET Core 2.0 への移行</span><span class="sxs-lookup"><span data-stu-id="c14ea-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="c14ea-104">[Scott Addie](https://github.com/scottaddie)および[hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="c14ea-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="c14ea-105">ASP.NET Core 2.0 には、認証のための新しいモデルが用意されており、 [Identity](xref:security/authentication/identity) サービスを使用した構成が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="c14ea-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="c14ea-106">認証を使用するか Identity 、次に示すように、新しいモデルを使用するように更新することができる ASP.NET Core 1.x アプリケーション。</span><span class="sxs-lookup"><span data-stu-id="c14ea-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="c14ea-107">名前空間の更新</span><span class="sxs-lookup"><span data-stu-id="c14ea-107">Update namespaces</span></span>

<span data-ttu-id="c14ea-108">1.x では、やなどのクラスは `IdentityRole` `IdentityUser` 名前空間にありました `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="c14ea-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="c14ea-109">2.0 では、 <xref:Microsoft.AspNetCore.Identity> このようなクラスのいくつかの新しいホームに名前空間が追加されました。</span><span class="sxs-lookup"><span data-stu-id="c14ea-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="c14ea-110">既定のコードでは Identity 、影響を受けるクラスにはおよびが含ま `ApplicationUser` `Startup` れます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="c14ea-111">ステートメントを調整して `using` 、影響を受ける参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="c14ea-112">認証ミドルウェアとサービス</span><span class="sxs-lookup"><span data-stu-id="c14ea-112">Authentication Middleware and services</span></span>

<span data-ttu-id="c14ea-113">1.x プロジェクトでは、認証はミドルウェアを介して構成されます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="c14ea-114">ミドルウェアメソッドは、サポートする各認証スキームに対して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="c14ea-115">次の 1. x の例では、 Identity *Startup.cs*で Facebook 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="c14ea-116">2.0 プロジェクトでは、認証はサービスを介して構成されます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="c14ea-117">各認証スキームは、 `ConfigureServices` *Startup.cs*のメソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="c14ea-118">`UseIdentity`メソッドはに置き換えられ `UseAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="c14ea-119">次の2.0 の例では、Startup.cs ので Facebook 認証を構成し Identity ます。 *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="c14ea-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="c14ea-120">メソッドは、 `UseAuthentication` 認証ミドルウェアコンポーネントを1つ追加します。これは、自動認証とリモート認証要求の処理を担当します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="c14ea-121">個々のミドルウェアコンポーネントはすべて、単一の共通ミドルウェアコンポーネントに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="c14ea-122">次に、各主要な認証スキームの2.0 移行手順を示します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="c14ea-123">Cookie ベースの認証</span><span class="sxs-lookup"><span data-stu-id="c14ea-123">Cookie-based authentication</span></span>

<span data-ttu-id="c14ea-124">次の2つのオプションのいずれかを選択し、 *Startup.cs*で必要な変更を行います。</span><span class="sxs-lookup"><span data-stu-id="c14ea-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="c14ea-125">Cookie を使用するIdentity</span><span class="sxs-lookup"><span data-stu-id="c14ea-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="c14ea-126">`UseIdentity` `UseAuthentication` メソッド内のをに置き換え `Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="c14ea-127">メソッドでメソッドを呼び出して、 `AddIdentity` `ConfigureServices` cookie 認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="c14ea-128">必要に応じて、 `ConfigureApplicationCookie` メソッドでメソッドまたはメソッドを呼び出して、 `ConfigureExternalCookie` `ConfigureServices` cookie の設定を調整し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="c14ea-129">Cookie を使用しないIdentity</span><span class="sxs-lookup"><span data-stu-id="c14ea-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="c14ea-130">メソッドの `UseCookieAuthentication` メソッド呼び出しを `Configure` 次のように置き換え `UseAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="c14ea-131">`AddAuthentication`メソッドでメソッドとメソッドを呼び出し `AddCookie` `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="c14ea-132">JWT ベアラー認証</span><span class="sxs-lookup"><span data-stu-id="c14ea-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="c14ea-133">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="c14ea-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="c14ea-134">メソッドの `UseJwtBearerAuthentication` メソッド呼び出しを `Configure` 次のように置き換え `UseAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c14ea-135">`AddJwtBearer`メソッドでメソッドを呼び出し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="c14ea-136">このコードスニペットはを使用しない Identity ため、既定のスキームはメソッドに渡すことによって設定する必要があり `JwtBearerDefaults.AuthenticationScheme` `AddAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="c14ea-137">OpenID Connect (OIDC) 認証</span><span class="sxs-lookup"><span data-stu-id="c14ea-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="c14ea-138">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="c14ea-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="c14ea-139">メソッドの `UseOpenIdConnectAuthentication` メソッド呼び出しを `Configure` 次のように置き換え `UseAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c14ea-140">`AddOpenIdConnect`メソッドでメソッドを呼び出し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="c14ea-141">アクションの `PostLogoutRedirectUri` プロパティを `OpenIdConnectOptions` 次のように置き換え `SignedOutRedirectUri` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="c14ea-142">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="c14ea-142">Facebook authentication</span></span>

<span data-ttu-id="c14ea-143">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="c14ea-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="c14ea-144">メソッドの `UseFacebookAuthentication` メソッド呼び出しを `Configure` 次のように置き換え `UseAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c14ea-145">`AddFacebook`メソッドでメソッドを呼び出し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="c14ea-146">Google での認証</span><span class="sxs-lookup"><span data-stu-id="c14ea-146">Google authentication</span></span>

<span data-ttu-id="c14ea-147">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="c14ea-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="c14ea-148">メソッドの `UseGoogleAuthentication` メソッド呼び出しを `Configure` 次のように置き換え `UseAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c14ea-149">`AddGoogle`メソッドでメソッドを呼び出し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="c14ea-150">Microsoft アカウント認証</span><span class="sxs-lookup"><span data-stu-id="c14ea-150">Microsoft Account authentication</span></span>

<span data-ttu-id="c14ea-151">Microsoft アカウント認証の詳細については、 [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/14455)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c14ea-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="c14ea-152">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="c14ea-152">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="c14ea-153">メソッドの `UseMicrosoftAccountAuthentication` メソッド呼び出しを `Configure` 次のように置き換え `UseAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c14ea-154">`AddMicrosoftAccount`メソッドでメソッドを呼び出し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="c14ea-155">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="c14ea-155">Twitter authentication</span></span>

<span data-ttu-id="c14ea-156">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="c14ea-156">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="c14ea-157">メソッドの `UseTwitterAuthentication` メソッド呼び出しを `Configure` 次のように置き換え `UseAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c14ea-158">`AddTwitter`メソッドでメソッドを呼び出し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="c14ea-159">既定の認証方式を設定する</span><span class="sxs-lookup"><span data-stu-id="c14ea-159">Setting default authentication schemes</span></span>

<span data-ttu-id="c14ea-160">1.x では、 `AutomaticAuthenticate` `AutomaticChallenge` [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1)基本クラスのプロパティとプロパティが、1つの認証スキームで設定されることを意図していました。</span><span class="sxs-lookup"><span data-stu-id="c14ea-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="c14ea-161">これを実施するための適切な方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="c14ea-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="c14ea-162">2.0 では、これら2つのプロパティは個別のインスタンスのプロパティとして削除されてい `AuthenticationOptions` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="c14ea-163">これらは、 `AddAuthentication` Startup.cs のメソッド内のメソッド呼び出しで構成でき `ConfigureServices` ます。 *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="c14ea-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="c14ea-164">前のコードスニペットでは、既定のスキームは `CookieAuthenticationDefaults.AuthenticationScheme` ("cookie") に設定されています。</span><span class="sxs-lookup"><span data-stu-id="c14ea-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="c14ea-165">または、オーバーロードされたバージョンのメソッドを使用して、複数の `AddAuthentication` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="c14ea-166">次のオーバーロードされたメソッドの例では、既定のスキームはに設定されて `CookieAuthenticationDefaults.AuthenticationScheme` います。</span><span class="sxs-lookup"><span data-stu-id="c14ea-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="c14ea-167">認証方式は、個々の `[Authorize]` 属性または承認ポリシー内で指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="c14ea-168">次のいずれかの条件に該当する場合は、2.0 で既定のスキームを定義します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="c14ea-169">ユーザーが自動的にサインインするようにするには</span><span class="sxs-lookup"><span data-stu-id="c14ea-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="c14ea-170">`[Authorize]`スキーマを指定せずに属性または承認ポリシーを使用する</span><span class="sxs-lookup"><span data-stu-id="c14ea-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="c14ea-171">この規則の例外は `AddIdentity` メソッドです。</span><span class="sxs-lookup"><span data-stu-id="c14ea-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="c14ea-172">このメソッドは、cookie を追加し、既定の認証およびチャレンジスキームをアプリケーションクッキーに設定し `IdentityConstants.ApplicationScheme` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="c14ea-173">また、既定のサインインスキームを外部 cookie に設定し `IdentityConstants.ExternalScheme` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="c14ea-174">HttpContext 認証拡張機能の使用</span><span class="sxs-lookup"><span data-stu-id="c14ea-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="c14ea-175">`IAuthenticationManager`インターフェイスは、1. x 認証システムへのメインエントリポイントです。</span><span class="sxs-lookup"><span data-stu-id="c14ea-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="c14ea-176">名前空間の新しい拡張メソッドのセットに置き換えられました `HttpContext` `Microsoft.AspNetCore.Authentication` 。</span><span class="sxs-lookup"><span data-stu-id="c14ea-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="c14ea-177">たとえば、1. x プロジェクトはプロパティを参照します。 `Authentication`</span><span class="sxs-lookup"><span data-stu-id="c14ea-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="c14ea-178">2.0 プロジェクトで、 `Microsoft.AspNetCore.Authentication` 名前空間をインポートし、プロパティ参照を削除し `Authentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="c14ea-179">Windows 認証 (HTTP.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="c14ea-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="c14ea-180">Windows 認証には、次の2つのバリエーションがあります。</span><span class="sxs-lookup"><span data-stu-id="c14ea-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="c14ea-181">ホストは、認証されたユーザーのみを許可します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-181">The host only allows authenticated users.</span></span> <span data-ttu-id="c14ea-182">このバリエーションは、2.0 の変更の影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="c14ea-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="c14ea-183">ホストは、匿名ユーザーと認証済みユーザーの両方を許可します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="c14ea-184">このバリエーションは、2.0 の変更の影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="c14ea-185">たとえば、アプリでは、 [IIS](xref:host-and-deploy/iis/index)または[HTTP.sys](xref:fundamentals/servers/httpsys)レイヤーで匿名ユーザーを許可する必要がありますが、コントローラーレベルでユーザーを承認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c14ea-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="c14ea-186">このシナリオでは、メソッドで既定のスキームを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="c14ea-187">[AspNetCore 統合](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)では、既定のスキームを次のように設定します。 `IISDefaults.AuthenticationScheme`</span><span class="sxs-lookup"><span data-stu-id="c14ea-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="c14ea-188">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)の場合は、既定のスキームを次のように設定します。 `HttpSysDefaults.AuthenticationScheme`</span><span class="sxs-lookup"><span data-stu-id="c14ea-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="c14ea-189">既定のスキームを設定しなかった場合は、次の例外で承認 (チャレンジ) 要求を処理できなくなります。</span><span class="sxs-lookup"><span data-stu-id="c14ea-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="c14ea-190">`System.InvalidOperationException`: AuthenticationScheme が指定されていません。 DefaultChallengeScheme が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="c14ea-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="c14ea-191">詳細については、「<xref:security/authentication/windowsauth>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c14ea-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="c14ea-192">Identity Cookieoptions インスタンス</span><span class="sxs-lookup"><span data-stu-id="c14ea-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="c14ea-193">2.0 の変更の副作用は、cookie オプションのインスタンスの代わりに名前付きオプションを使用するように切り替えることです。</span><span class="sxs-lookup"><span data-stu-id="c14ea-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="c14ea-194">Cookie スキーム名をカスタマイズする機能 Identity は削除されます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="c14ea-195">たとえば、1.x プロジェクトでは、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用し `IdentityCookieOptions` て、パラメーターを*AccountController.cs*および*ManageController.cs*に渡します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="c14ea-196">外部クッキー認証スキームは、指定されたインスタンスからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="c14ea-197">上記のコンストラクターインジェクションは、2.0 プロジェクトでは不要になり、 `_externalCookieScheme` フィールドを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="c14ea-198">1.x プロジェクトでは、次のようにフィールドが使用されていました。 `_externalCookieScheme`</span><span class="sxs-lookup"><span data-stu-id="c14ea-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="c14ea-199">2.0 プロジェクトで、上記のコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="c14ea-200">定数は、 `IdentityConstants.ExternalScheme` 直接使用できます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="c14ea-201">次の名前空間をインポートして、新しく追加された呼び出しを解決し `SignOutAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="c14ea-202">ユーザーの POCO ナビゲーションプロパティの追加</span><span class="sxs-lookup"><span data-stu-id="c14ea-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="c14ea-203">基本 POCO (Plain Old CLR Object) の Entity Framework (EF) コアナビゲーションプロパティ `IdentityUser` が削除されました。</span><span class="sxs-lookup"><span data-stu-id="c14ea-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="c14ea-204">1.x プロジェクトでこれらのプロパティを使用していた場合は、手動で2.0 プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="c14ea-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="c14ea-205">EF Core 移行の実行時に外部キーが重複しないようにするには、 `IdentityDbContext` クラスの `OnModelCreating` メソッド (呼び出しの後) に次のを追加し `base.OnModelCreating();` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="c14ea-206">GetExternalAuthenticationSchemes の置換</span><span class="sxs-lookup"><span data-stu-id="c14ea-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="c14ea-207">`GetExternalAuthenticationSchemes`非同期バージョンを優先するため、同期メソッドが削除されました。</span><span class="sxs-lookup"><span data-stu-id="c14ea-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="c14ea-208">1.x プロジェクトでは、 *Controllers/ManageController*に次のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c14ea-208">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="c14ea-209">このメソッドは*Views/Account/Login. cshtml*にも表示されます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="c14ea-210">2.0 プロジェクトでは、メソッドを使用し <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="c14ea-211">*ManageController.cs*の変更は、次のコードのようになります。</span><span class="sxs-lookup"><span data-stu-id="c14ea-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="c14ea-212">*Login. cshtml*で、 `AuthenticationScheme` ループでアクセスされるプロパティが `foreach` 次のように変更されます。 `Name`</span><span class="sxs-lookup"><span data-stu-id="c14ea-212">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="c14ea-213">ManageLoginsViewModel プロパティの変更</span><span class="sxs-lookup"><span data-stu-id="c14ea-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="c14ea-214">`ManageLoginsViewModel`オブジェクトは、 `ManageLogins` *ManageController.cs*のアクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="c14ea-215">1.x プロジェクトでは、オブジェクトのプロパティの `OtherLogins` 戻り値の型は `IList<AuthenticationDescription>` です。</span><span class="sxs-lookup"><span data-stu-id="c14ea-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="c14ea-216">この戻り値の型をインポートするには、次のものが必要です `Microsoft.AspNetCore.Http.Authentication` 。</span><span class="sxs-lookup"><span data-stu-id="c14ea-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="c14ea-217">2.0 プロジェクトでは、戻り値の型はに変更され `IList<AuthenticationScheme>` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="c14ea-218">この新しい戻り値の型では、インポートをインポートに置き換える必要があり `Microsoft.AspNetCore.Http.Authentication` `Microsoft.AspNetCore.Authentication` ます。</span><span class="sxs-lookup"><span data-stu-id="c14ea-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="c14ea-219">その他の資料</span><span class="sxs-lookup"><span data-stu-id="c14ea-219">Additional resources</span></span>

<span data-ttu-id="c14ea-220">詳細については、GitHub で[の Auth 2.0 の問題の説明](https://github.com/aspnet/Security/issues/1338)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c14ea-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
