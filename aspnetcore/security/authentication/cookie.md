---
title: 認証を使用 cookie しない ASP.NET Core Identity
author: rick-anderson
description: を使用せずに認証を使用する方法について説明 cookie ASP.NET Core Identity します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 04d2f0d289e2c9ec13aeb880df47240bec19d3ec
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876764"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="1c32d-103">認証を使用 cookie しない ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1c32d-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="1c32d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1c32d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1c32d-105">ASP.NET Core Identity は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="1c32d-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="1c32d-106">ただし、を cookie 使用しないベースの認証プロバイダーを ASP.NET Core Identity 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="1c32d-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="1c32d-107">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1c32d-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="1c32d-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1c32d-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1c32d-109">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="1c32d-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="1c32d-110">**電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="1c32d-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="1c32d-111">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="1c32d-112">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="1c32d-113">構成</span><span class="sxs-lookup"><span data-stu-id="1c32d-113">Configuration</span></span>

<span data-ttu-id="1c32d-114">アプリで [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore Cookie のプロジェクトファイルにパッケージ参照を作成します。s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="1c32d-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="1c32d-115">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="1c32d-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="1c32d-117">`AuthenticationScheme` は、認証の複数のインスタンスがあり cookie 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="1c32d-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="1c32d-118">`AuthenticationScheme`を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 Cookie</span><span class="sxs-lookup"><span data-stu-id="1c32d-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="1c32d-119">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="1c32d-120">アプリの認証方式は、アプリの認証スキームとは異なり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="1c32d-121">cookieに認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="1c32d-122">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="1c32d-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="1c32d-123">cookieサイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="1c32d-124">詳細については、<xref:security/gdpr#essential-cookies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1c32d-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="1c32d-125">で、とを呼び出して、 `Startup.Configure` `UseAuthentication` プロパティを `UseAuthorization` 設定し、 `HttpContext.User` 要求の承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="1c32d-126">`UseAuthentication`を `UseAuthorization` 呼び出す前に、メソッドとメソッドを呼び出し `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="1c32d-127">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="1c32d-128">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="1c32d-129">Cookie ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="1c32d-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="1c32d-130">ポリシー [ Cookie ミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)は cookie 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="1c32d-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="1c32d-131">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="1c32d-132"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>ポリシーミドルウェアに提供されるを使用して、 Cookie cookie cookie cookie が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="1c32d-133">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="1c32d-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="1c32d-134">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="1c32d-135">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 cookie クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="1c32d-136">Cookieのポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `Cookie.SameSite` `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="1c32d-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="1c32d-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="1c32d-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="1c32d-138">Cookie.SameSite</span></span> | <span data-ttu-id="1c32d-139">結果 Cookie 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="1c32d-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="1c32d-140">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-140">SameSiteMode.None</span></span>     | <span data-ttu-id="1c32d-141">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-141">SameSiteMode.None</span></span><br><span data-ttu-id="1c32d-142">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-143">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="1c32d-144">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-144">SameSiteMode.None</span></span><br><span data-ttu-id="1c32d-145">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-146">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="1c32d-147">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="1c32d-148">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-148">SameSiteMode.None</span></span><br><span data-ttu-id="1c32d-149">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-150">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="1c32d-151">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-152">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-153">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="1c32d-154">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="1c32d-155">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-155">SameSiteMode.None</span></span><br><span data-ttu-id="1c32d-156">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-157">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="1c32d-158">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="1c32d-159">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="1c32d-160">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="1c32d-161">認証を作成する cookie</span><span class="sxs-lookup"><span data-stu-id="1c32d-161">Create an authentication cookie</span></span>

<span data-ttu-id="1c32d-162">保持しているユーザー情報を作成するに cookie は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="1c32d-163">ユーザー情報はにシリアル化され、に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="1c32d-164">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="1c32d-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="1c32d-165">`SignInAsync` 暗号化されたを作成 cookie し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="1c32d-166">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="1c32d-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> は、既定ではいくつかの特定のパス (ログインパスやログアウトパスなど) でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="1c32d-168">詳細については、「 [ Cookie authenticationhandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1c32d-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="1c32d-169">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="1c32d-170">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="1c32d-171">サインアウト</span><span class="sxs-lookup"><span data-stu-id="1c32d-171">Sign out</span></span>

<span data-ttu-id="1c32d-172">現在のユーザーをサインアウトして削除するには cookie 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="1c32d-173">`CookieAuthenticationDefaults.AuthenticationScheme`(または " Cookie s") がスキームとして使用されていない場合 ("Contoso Cookie " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="1c32d-174">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-174">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="1c32d-175">サーバーは、クライアントのブラウザーを制御しません。</span><span class="sxs-lookup"><span data-stu-id="1c32d-175">The server has no control of the clients browser.</span></span> <span data-ttu-id="1c32d-176">ユーザーがブラウザーまたはタブを閉じた場合、サーバーはユーザーをサインアウトできません。</span><span class="sxs-lookup"><span data-stu-id="1c32d-176">If the user closes the browser or tab, the server cannot sign out the user.</span></span> <span data-ttu-id="1c32d-177">ブラウザーが閉じられたときにユーザーへのサインアウトを実装するには、JavaScript でを検出する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-177">To implement signing out the user when the browser is closed, you must detect that with JavaScript.</span></span> <span data-ttu-id="1c32d-178">「ブラウザーウィンドウのタブを閉じるイベントを検出する方法」を検索します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-178">Search for "How to Detect Browser Window Tab Close Event?".</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="1c32d-179">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="1c32d-179">React to back-end changes</span></span>

<span data-ttu-id="1c32d-180">が作成されると、は cookie cookie id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-180">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="1c32d-181">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-181">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="1c32d-182">アプリの cookie 認証システムは、認証に基づいて要求を処理し続け cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-182">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="1c32d-183">認証が有効である限り、ユーザーはアプリにサインインしたままに cookie なります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-183">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="1c32d-184">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-184">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="1c32d-185">すべての要求でを検証すると、 cookie アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-185">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="1c32d-186">検証の方法 cookie の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="1c32d-186">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="1c32d-187">ユーザーの発行後にデータベースが変更されていない場合は、 cookie ユーザー cookie がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1c32d-187">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="1c32d-188">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-188">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="1c32d-189">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-189">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="1c32d-190">値に基づいてデータベースが変更されたときにを無効にするには、 cookie データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し cookie `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-190">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="1c32d-191">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-191">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="1c32d-192">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-192">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="1c32d-193">サービス登録時にイベントインスタンスを cookie メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-193">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1c32d-194">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-194">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="1c32d-195">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-195">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="1c32d-196">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-196">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="1c32d-197">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-197">The approach described here is triggered on every request.</span></span> <span data-ttu-id="1c32d-198">cookieすべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-198">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="1c32d-199">永続的な cookie s</span><span class="sxs-lookup"><span data-stu-id="1c32d-199">Persistent cookies</span></span>

<span data-ttu-id="1c32d-200">cookieをブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1c32d-200">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="1c32d-201">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="1c32d-201">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="1c32d-202">次のコードスニペットは、 cookie ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-202">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="1c32d-203">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-203">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="1c32d-204">ブラウザーを閉じている間にが cookie 期限切れになると、ブラウザーは再起動後にをクリアし cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-204">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="1c32d-205"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-205">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="1c32d-206">絶対 cookie 有効期限</span><span class="sxs-lookup"><span data-stu-id="1c32d-206">Absolute cookie expiration</span></span>

<span data-ttu-id="1c32d-207">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-207">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="1c32d-208">永続的なを作成するには cookie 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-208">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="1c32d-209">それ以外の場合、は cookie セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-209">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="1c32d-210">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="1c32d-210">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="1c32d-211">次のコードスニペットでは、 cookie 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-211">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="1c32d-212">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-212">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1c32d-213">ASP.NET Core Identity は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="1c32d-213">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="1c32d-214">ただし、を cookie 使用しないベースの認証プロバイダーを ASP.NET Core Identity 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="1c32d-214">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="1c32d-215">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1c32d-215">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="1c32d-216">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1c32d-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1c32d-217">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="1c32d-217">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="1c32d-218">**電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="1c32d-218">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="1c32d-219">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-219">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="1c32d-220">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-220">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="1c32d-221">構成</span><span class="sxs-lookup"><span data-stu-id="1c32d-221">Configuration</span></span>

<span data-ttu-id="1c32d-222">アプリで [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore Cookie のプロジェクトファイルにパッケージ参照を作成します。s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="1c32d-222">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="1c32d-223">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-223">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="1c32d-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="1c32d-225">`AuthenticationScheme` は、認証の複数のインスタンスがあり cookie 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="1c32d-225">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="1c32d-226">`AuthenticationScheme`を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 Cookie</span><span class="sxs-lookup"><span data-stu-id="1c32d-226">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="1c32d-227">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-227">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="1c32d-228">アプリの認証方式は、アプリの認証スキームとは異なり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-228">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="1c32d-229">cookieに認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-229">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="1c32d-230">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="1c32d-230">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="1c32d-231">cookieサイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-231">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="1c32d-232">詳細については、<xref:security/gdpr#essential-cookies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1c32d-232">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="1c32d-233">メソッドで、 `Startup.Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-233">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="1c32d-234">`UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-234">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="1c32d-235">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-235">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="1c32d-236">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-236">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="1c32d-237">Cookie ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="1c32d-237">Cookie Policy Middleware</span></span>

<span data-ttu-id="1c32d-238">ポリシー [ Cookie ミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)は cookie 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="1c32d-238">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="1c32d-239">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-239">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="1c32d-240"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>ポリシーミドルウェアに提供されるを使用して、 Cookie cookie cookie cookie が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-240">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="1c32d-241">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="1c32d-241">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="1c32d-242">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-242">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="1c32d-243">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 cookie クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-243">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="1c32d-244">Cookieのポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `Cookie.SameSite` `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-244">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="1c32d-245">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="1c32d-245">MinimumSameSitePolicy</span></span> | <span data-ttu-id="1c32d-246">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="1c32d-246">Cookie.SameSite</span></span> | <span data-ttu-id="1c32d-247">結果 Cookie 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="1c32d-247">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="1c32d-248">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-248">SameSiteMode.None</span></span>     | <span data-ttu-id="1c32d-249">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-249">SameSiteMode.None</span></span><br><span data-ttu-id="1c32d-250">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-251">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-251">SameSiteMode.Strict</span></span> | <span data-ttu-id="1c32d-252">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-252">SameSiteMode.None</span></span><br><span data-ttu-id="1c32d-253">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-254">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-254">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="1c32d-255">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-255">SameSiteMode.Lax</span></span>      | <span data-ttu-id="1c32d-256">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-256">SameSiteMode.None</span></span><br><span data-ttu-id="1c32d-257">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-258">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-258">SameSiteMode.Strict</span></span> | <span data-ttu-id="1c32d-259">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-259">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-260">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-261">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-261">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="1c32d-262">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-262">SameSiteMode.Strict</span></span>   | <span data-ttu-id="1c32d-263">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="1c32d-263">SameSiteMode.None</span></span><br><span data-ttu-id="1c32d-264">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="1c32d-264">SameSiteMode.Lax</span></span><br><span data-ttu-id="1c32d-265">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-265">SameSiteMode.Strict</span></span> | <span data-ttu-id="1c32d-266">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-266">SameSiteMode.Strict</span></span><br><span data-ttu-id="1c32d-267">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-267">SameSiteMode.Strict</span></span><br><span data-ttu-id="1c32d-268">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="1c32d-268">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="1c32d-269">認証を作成する cookie</span><span class="sxs-lookup"><span data-stu-id="1c32d-269">Create an authentication cookie</span></span>

<span data-ttu-id="1c32d-270">保持しているユーザー情報を作成するに cookie は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-270">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="1c32d-271">ユーザー情報はにシリアル化され、に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-271">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="1c32d-272">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="1c32d-272">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="1c32d-273">`SignInAsync` 暗号化されたを作成 cookie し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-273">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="1c32d-274">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-274">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="1c32d-275">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-275">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="1c32d-276">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-276">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="1c32d-277">サインアウト</span><span class="sxs-lookup"><span data-stu-id="1c32d-277">Sign out</span></span>

<span data-ttu-id="1c32d-278">現在のユーザーをサインアウトして削除するには cookie 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-278">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="1c32d-279">`CookieAuthenticationDefaults.AuthenticationScheme`(または " Cookie s") がスキームとして使用されていない場合 ("Contoso Cookie " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-279">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="1c32d-280">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-280">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="1c32d-281">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="1c32d-281">React to back-end changes</span></span>

<span data-ttu-id="1c32d-282">が作成されると、は cookie cookie id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-282">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="1c32d-283">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-283">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="1c32d-284">アプリの cookie 認証システムは、認証に基づいて要求を処理し続け cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-284">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="1c32d-285">認証が有効である限り、ユーザーはアプリにサインインしたままに cookie なります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-285">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="1c32d-286">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-286">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="1c32d-287">すべての要求でを検証すると、 cookie アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-287">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="1c32d-288">検証の方法 cookie の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="1c32d-288">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="1c32d-289">ユーザーの発行後にデータベースが変更されていない場合は、 cookie ユーザー cookie がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1c32d-289">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="1c32d-290">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-290">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="1c32d-291">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-291">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="1c32d-292">値に基づいてデータベースが変更されたときにを無効にするには、 cookie データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し cookie `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-292">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="1c32d-293">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-293">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="1c32d-294">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-294">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="1c32d-295">サービス登録時にイベントインスタンスを cookie メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-295">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1c32d-296">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-296">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="1c32d-297">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-297">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="1c32d-298">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-298">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="1c32d-299">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-299">The approach described here is triggered on every request.</span></span> <span data-ttu-id="1c32d-300">cookieすべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-300">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="1c32d-301">永続的な cookie s</span><span class="sxs-lookup"><span data-stu-id="1c32d-301">Persistent cookies</span></span>

<span data-ttu-id="1c32d-302">cookieをブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1c32d-302">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="1c32d-303">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="1c32d-303">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="1c32d-304">次のコードスニペットは、 cookie ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-304">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="1c32d-305">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-305">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="1c32d-306">ブラウザーを閉じている間にが cookie 期限切れになると、ブラウザーは再起動後にをクリアし cookie ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-306">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="1c32d-307"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-307">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="1c32d-308">絶対 cookie 有効期限</span><span class="sxs-lookup"><span data-stu-id="1c32d-308">Absolute cookie expiration</span></span>

<span data-ttu-id="1c32d-309">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-309">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="1c32d-310">永続的なを作成するには cookie 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-310">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="1c32d-311">それ以外の場合、は cookie セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1c32d-311">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="1c32d-312">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="1c32d-312">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="1c32d-313">次のコードスニペットでは、 cookie 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="1c32d-313">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="1c32d-314">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="1c32d-314">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1c32d-315">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="1c32d-315">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="1c32d-316">ポリシーベースのロールチェック</span><span class="sxs-lookup"><span data-stu-id="1c32d-316">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
