---
title: 認証とIdentity ASP.NET Core 2.0 への移行
author: scottaddie
description: この記事では ASP.NET Core 1.x 認証とIdentity ASP.NET Core 2.0 に移行するための最も一般的な手順について説明します。
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: e828446716d88d92aeb587874421a5751dcb6de0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769502"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="eb6ce-103">認証とIdentity ASP.NET Core 2.0 への移行</span><span class="sxs-lookup"><span data-stu-id="eb6ce-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="eb6ce-104">[Scott Addie](https://github.com/scottaddie)および[hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="eb6ce-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="eb6ce-105">ASP.NET Core 2.0 には、認証のための[Identity](xref:security/authentication/identity)新しいモデルが用意されており、サービスを使用した構成が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="eb6ce-106">認証を使用するかIdentity 、次に示すように、新しいモデルを使用するように更新することができる ASP.NET Core 1.x アプリケーション。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="eb6ce-107">名前空間の更新</span><span class="sxs-lookup"><span data-stu-id="eb6ce-107">Update namespaces</span></span>

<span data-ttu-id="eb6ce-108">1.x では`IdentityRole` `IdentityUser` `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 、やなどのクラスは名前空間にありました。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="eb6ce-109">2.0 では、 <xref:Microsoft.AspNetCore.Identity>このようなクラスのいくつかの新しいホームに名前空間が追加されました。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="eb6ce-110">既定Identityのコードでは、影響を`ApplicationUser`受ける`Startup`クラスにはおよびが含まれます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="eb6ce-111">ステートメントを`using`調整して、影響を受ける参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="eb6ce-112">認証ミドルウェアとサービス</span><span class="sxs-lookup"><span data-stu-id="eb6ce-112">Authentication Middleware and services</span></span>

<span data-ttu-id="eb6ce-113">1.x プロジェクトでは、認証はミドルウェアを介して構成されます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="eb6ce-114">ミドルウェアメソッドは、サポートする各認証スキームに対して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="eb6ce-115">次の 1. x の例では、 Identity *Startup.cs*で Facebook 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="eb6ce-116">2.0 プロジェクトでは、認証はサービスを介して構成されます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="eb6ce-117">各認証スキームは、 `ConfigureServices` *Startup.cs*のメソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="eb6ce-118">`UseIdentity`メソッドはに`UseAuthentication`置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="eb6ce-119">次の2.0 の例では、 Identity *Startup.cs*ので Facebook 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="eb6ce-120">メソッド`UseAuthentication`は、認証ミドルウェアコンポーネントを1つ追加します。これは、自動認証とリモート認証要求の処理を担当します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="eb6ce-121">個々のミドルウェアコンポーネントはすべて、単一の共通ミドルウェアコンポーネントに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="eb6ce-122">次に、各主要な認証スキームの2.0 移行手順を示します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="eb6ce-123">Cookie ベースの認証</span><span class="sxs-lookup"><span data-stu-id="eb6ce-123">Cookie-based authentication</span></span>

<span data-ttu-id="eb6ce-124">次の2つのオプションのいずれかを選択し、 *Startup.cs*で必要な変更を行います。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="eb6ce-125">Cookie を使用するIdentity</span><span class="sxs-lookup"><span data-stu-id="eb6ce-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="eb6ce-126">メソッド内のをに`UseIdentity` `UseAuthentication`置き換えます。 `Configure`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="eb6ce-127">`ConfigureServices`メソッドで`AddIdentity`メソッドを呼び出して、cookie 認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="eb6ce-128">必要に応じて`ConfigureApplicationCookie` 、 `ConfigureExternalCookie` `ConfigureServices`メソッドでメソッドまたはメソッドをIdentity呼び出して、cookie の設定を調整します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="eb6ce-129">Cookie を使用しないIdentity</span><span class="sxs-lookup"><span data-stu-id="eb6ce-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="eb6ce-130">メソッドの`UseCookieAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="eb6ce-131">メソッドで`AddAuthentication`メソッド`AddCookie`とメソッドを呼び出します。 `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="eb6ce-132">JWT ベアラー認証</span><span class="sxs-lookup"><span data-stu-id="eb6ce-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="eb6ce-133">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="eb6ce-134">メソッドの`UseJwtBearerAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="eb6ce-135">`ConfigureServices`メソッドで`AddJwtBearer`メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="eb6ce-136">このコードスニペットはをIdentity使用しないため、既定のスキームは`JwtBearerDefaults.AuthenticationScheme` `AddAuthentication`メソッドに渡すことによって設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="eb6ce-137">OpenID Connect (OIDC) 認証</span><span class="sxs-lookup"><span data-stu-id="eb6ce-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="eb6ce-138">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="eb6ce-139">メソッドの`UseOpenIdConnectAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="eb6ce-140">`ConfigureServices`メソッドで`AddOpenIdConnect`メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

- <span data-ttu-id="eb6ce-141">アクションの`PostLogoutRedirectUri`プロパティを次のよう`SignedOutRedirectUri`に置き換えます。 `OpenIdConnectOptions`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="eb6ce-142">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="eb6ce-142">Facebook authentication</span></span>

<span data-ttu-id="eb6ce-143">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="eb6ce-144">メソッドの`UseFacebookAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="eb6ce-145">`ConfigureServices`メソッドで`AddFacebook`メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="eb6ce-146">Google での認証</span><span class="sxs-lookup"><span data-stu-id="eb6ce-146">Google authentication</span></span>

<span data-ttu-id="eb6ce-147">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="eb6ce-148">メソッドの`UseGoogleAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="eb6ce-149">`ConfigureServices`メソッドで`AddGoogle`メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="eb6ce-150">Microsoft アカウント認証</span><span class="sxs-lookup"><span data-stu-id="eb6ce-150">Microsoft Account authentication</span></span>

<span data-ttu-id="eb6ce-151">Microsoft アカウント認証の詳細については、 [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/14455)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="eb6ce-152">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-152">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="eb6ce-153">メソッドの`UseMicrosoftAccountAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="eb6ce-154">`ConfigureServices`メソッドで`AddMicrosoftAccount`メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="eb6ce-155">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="eb6ce-155">Twitter authentication</span></span>

<span data-ttu-id="eb6ce-156">*Startup.cs*で次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-156">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="eb6ce-157">メソッドの`UseTwitterAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="eb6ce-158">`ConfigureServices`メソッドで`AddTwitter`メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="eb6ce-159">既定の認証方式を設定する</span><span class="sxs-lookup"><span data-stu-id="eb6ce-159">Setting default authentication schemes</span></span>

<span data-ttu-id="eb6ce-160">1.x では、 [Authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1)基本`AutomaticChallenge`クラスのプロパティ`AutomaticAuthenticate`とプロパティが、1つの認証スキームで設定されることを意図していました。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="eb6ce-161">これを実施するための適切な方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="eb6ce-162">2.0 では、これら2つのプロパティは個別`AuthenticationOptions`のインスタンスのプロパティとして削除されています。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="eb6ce-163">これらは、 *Startup.cs*の`ConfigureServices`メソッド`AddAuthentication`内のメソッド呼び出しで構成できます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="eb6ce-164">前のコードスニペットでは、既定のスキームは ( `CookieAuthenticationDefaults.AuthenticationScheme` "cookie") に設定されています。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="eb6ce-165">または、オーバーロードされたバージョン`AddAuthentication`のメソッドを使用して、複数のプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="eb6ce-166">次のオーバーロードされたメソッドの例では、既定`CookieAuthenticationDefaults.AuthenticationScheme`のスキームはに設定されています。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="eb6ce-167">認証方式は、個々`[Authorize]`の属性または承認ポリシー内で指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="eb6ce-168">次のいずれかの条件に該当する場合は、2.0 で既定のスキームを定義します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="eb6ce-169">ユーザーが自動的にサインインするようにするには</span><span class="sxs-lookup"><span data-stu-id="eb6ce-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="eb6ce-170">スキーマを指定`[Authorize]`せずに属性または承認ポリシーを使用する</span><span class="sxs-lookup"><span data-stu-id="eb6ce-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="eb6ce-171">この規則の例外は`AddIdentity`メソッドです。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="eb6ce-172">このメソッドは、cookie を追加し、既定の認証およびチャレンジスキームをアプリケーションクッキー `IdentityConstants.ApplicationScheme`に設定します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="eb6ce-173">また、既定のサインインスキームを外部 cookie `IdentityConstants.ExternalScheme`に設定します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="eb6ce-174">HttpContext 認証拡張機能の使用</span><span class="sxs-lookup"><span data-stu-id="eb6ce-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="eb6ce-175">`IAuthenticationManager`インターフェイスは、1. x 認証システムへのメインエントリポイントです。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="eb6ce-176">名前空間の新しい拡張メソッドの`HttpContext`セットに置き換えられました。 `Microsoft.AspNetCore.Authentication`</span><span class="sxs-lookup"><span data-stu-id="eb6ce-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="eb6ce-177">たとえば、1. x プロジェクトはプロパティを`Authentication`参照します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="eb6ce-178">2.0 プロジェクトで、 `Microsoft.AspNetCore.Authentication`名前空間をインポートし、 `Authentication`プロパティ参照を削除します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="eb6ce-179">Windows 認証 (http.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="eb6ce-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="eb6ce-180">Windows 認証には、次の2つのバリエーションがあります。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="eb6ce-181">ホストは、認証されたユーザーのみを許可します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-181">The host only allows authenticated users.</span></span> <span data-ttu-id="eb6ce-182">このバリエーションは、2.0 の変更の影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="eb6ce-183">ホストは、匿名ユーザーと認証済みユーザーの両方を許可します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="eb6ce-184">このバリエーションは、2.0 の変更の影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="eb6ce-185">たとえば、アプリでは[IIS](xref:host-and-deploy/iis/index) [または http.sys レイヤー](xref:fundamentals/servers/httpsys)の匿名ユーザーを許可しますが、コントローラーレベルでユーザーを承認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="eb6ce-186">このシナリオでは、 `Startup.ConfigureServices`メソッドで既定のスキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="eb6ce-187">[AspNetCore 統合](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)では、既定のスキームを次のように`IISDefaults.AuthenticationScheme`設定します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="eb6ce-188">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)の場合は、既定のスキームを次のように`HttpSysDefaults.AuthenticationScheme`設定します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="eb6ce-189">既定のスキームを設定しなかった場合は、次の例外で承認 (チャレンジ) 要求を処理できなくなります。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="eb6ce-190">`System.InvalidOperationException`: AuthenticationScheme が指定されていません。 DefaultChallengeScheme が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="eb6ce-191">詳細については、「<xref:security/authentication/windowsauth>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="eb6ce-192">Identity Cookieoptions インスタンス</span><span class="sxs-lookup"><span data-stu-id="eb6ce-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="eb6ce-193">2.0 の変更の副作用は、cookie オプションのインスタンスの代わりに名前付きオプションを使用するように切り替えることです。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="eb6ce-194">Identity Cookie スキーム名をカスタマイズする機能は削除されます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="eb6ce-195">たとえば、1.x プロジェクトでは、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用し`IdentityCookieOptions`て、パラメーターを*AccountController.cs*および*ManageController.cs*に渡します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="eb6ce-196">外部クッキー認証スキームは、指定されたインスタンスからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="eb6ce-197">上記のコンストラクターインジェクションは、2.0 プロジェクトでは不要に`_externalCookieScheme`なり、フィールドを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="eb6ce-198">1.x プロジェクトでは、次`_externalCookieScheme`のようにフィールドが使用されていました。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="eb6ce-199">2.0 プロジェクトで、上記のコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="eb6ce-200">定数`IdentityConstants.ExternalScheme`は、直接使用できます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="eb6ce-201">次の名前空間`SignOutAsync`をインポートして、新しく追加された呼び出しを解決します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="eb6ce-202">ユーザーの POCO ナビゲーションプロパティの追加</span><span class="sxs-lookup"><span data-stu-id="eb6ce-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="eb6ce-203">基本`IdentityUser` POCO (PLAIN Old CLR Object) の ENTITY FRAMEWORK (EF) コアナビゲーションプロパティが削除されました。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="eb6ce-204">1.x プロジェクトでこれらのプロパティを使用していた場合は、手動で2.0 プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="eb6ce-205">EF Core 移行の実行時に外部キーが重複しないようにする`IdentityDbContext`には`OnModelCreating` 、クラスのメソッド`base.OnModelCreating();` (呼び出しの後) に次のを追加します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="eb6ce-206">GetExternalAuthenticationSchemes の置換</span><span class="sxs-lookup"><span data-stu-id="eb6ce-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="eb6ce-207">非同期バージョンを`GetExternalAuthenticationSchemes`優先するため、同期メソッドが削除されました。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="eb6ce-208">1.x プロジェクトでは、 *Controllers/ManageController*に次のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-208">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="eb6ce-209">このメソッドは*Views/Account/Login. cshtml*にも表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="eb6ce-210">2.0 プロジェクトでは、 <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*>メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="eb6ce-211">*ManageController.cs*の変更は、次のコードのようになります。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="eb6ce-212">*Login. cshtml*で、 `AuthenticationScheme` `foreach`ループでアクセスされるプロパティが次`Name`のように変更されます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-212">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="eb6ce-213">ManageLoginsViewModel プロパティの変更</span><span class="sxs-lookup"><span data-stu-id="eb6ce-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="eb6ce-214">`ManageLoginsViewModel`オブジェクトは、 `ManageLogins` *ManageController.cs*のアクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="eb6ce-215">1.x プロジェクトでは、オブジェクトの`OtherLogins`プロパティの戻り値の型`IList<AuthenticationDescription>`はです。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="eb6ce-216">この戻り値の型をインポート`Microsoft.AspNetCore.Http.Authentication`するには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="eb6ce-217">2.0 プロジェクトでは、戻り値の型`IList<AuthenticationScheme>`はに変更されます。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="eb6ce-218">この新しい戻り値の型で`Microsoft.AspNetCore.Http.Authentication`は、インポート`Microsoft.AspNetCore.Authentication`をインポートに置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="eb6ce-219">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="eb6ce-219">Additional resources</span></span>

<span data-ttu-id="eb6ce-220">詳細については、GitHub で[の Auth 2.0 の問題の説明](https://github.com/aspnet/Security/issues/1338)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb6ce-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
