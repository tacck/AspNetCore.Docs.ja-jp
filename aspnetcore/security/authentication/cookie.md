---
title: '認証を使用 cookie しない ASP.NET Core Identity'
author: rick-anderson
description: 'を使用せずに認証を使用する方法について説明 cookie ASP.NET Core Identity します。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: 'security/authentication/cookie'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061354"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="155e3-103">認証を使用 cookie しない ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="155e3-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="155e3-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="155e3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="155e3-105">ASP.NET Core Identity は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="155e3-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="155e3-106">ただし、を cookie 使用しないベースの認証プロバイダーを ASP.NET Core Identity 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="155e3-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="155e3-107">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="155e3-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="155e3-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="155e3-109">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="155e3-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="155e3-110">**電子メール** アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="155e3-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="155e3-111">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="155e3-112">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="155e3-113">構成</span><span class="sxs-lookup"><span data-stu-id="155e3-113">Configuration</span></span>

<span data-ttu-id="155e3-114">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="155e3-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="155e3-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="155e3-116">`AuthenticationScheme` は、認証の複数のインスタンスがあり cookie 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="155e3-116">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="155e3-117">`AuthenticationScheme`を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 Cookie</span><span class="sxs-lookup"><span data-stu-id="155e3-117">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="155e3-118">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="155e3-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="155e3-119">アプリの認証方式は、アプリの認証スキームとは異なり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-119">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="155e3-120">cookieに認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-120">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="155e3-121">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="155e3-121">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="155e3-122">cookieサイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-122">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="155e3-123">詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-123">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="155e3-124">で、とを呼び出して、 `Startup.Configure` `UseAuthentication` プロパティを `UseAuthorization` 設定し、 `HttpContext.User` 要求の承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="155e3-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="155e3-125">`UseAuthentication`を `UseAuthorization` 呼び出す前に、メソッドとメソッドを呼び出し `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="155e3-126">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-126">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="155e3-127">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-127">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="155e3-128">Cookie ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="155e3-128">Cookie Policy Middleware</span></span>

<span data-ttu-id="155e3-129">ポリシー [ Cookie ミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)は cookie 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="155e3-129">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="155e3-130">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="155e3-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="155e3-131"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>ポリシーミドルウェアに提供されるを使用して、 Cookie cookie cookie cookie が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="155e3-131">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="155e3-132">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="155e3-132">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="155e3-133">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="155e3-134">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 cookie クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="155e3-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="155e3-135">Cookieのポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `Cookie.SameSite` `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="155e3-135">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="155e3-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="155e3-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="155e3-137">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="155e3-137">Cookie.SameSite</span></span> | <span data-ttu-id="155e3-138">結果 Cookie 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="155e3-138">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="155e3-139">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-139">SameSiteMode.None</span></span>     | <span data-ttu-id="155e3-140">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-140">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-141">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-142">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-143">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-143">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-144">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-145">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="155e3-146">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="155e3-147">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-147">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-148">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-149">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-150">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-151">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-152">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="155e3-153">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="155e3-154">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-154">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-155">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-156">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-157">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="155e3-158">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="155e3-159">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="155e3-160">認証を作成する cookie</span><span class="sxs-lookup"><span data-stu-id="155e3-160">Create an authentication cookie</span></span>

<span data-ttu-id="155e3-161">保持しているユーザー情報を作成するに cookie は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="155e3-161">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="155e3-162">ユーザー情報はにシリアル化され、に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-162">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="155e3-163">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="155e3-163">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="155e3-164">`SignInAsync` 暗号化されたを作成 cookie し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="155e3-164">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="155e3-165">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="155e3-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> は、既定ではいくつかの特定のパス (ログインパスやログアウトパスなど) でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="155e3-167">詳細については、「 [ Cookie authenticationhandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-167">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="155e3-168">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="155e3-169">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="155e3-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="155e3-170">サインアウト</span><span class="sxs-lookup"><span data-stu-id="155e3-170">Sign out</span></span>

<span data-ttu-id="155e3-171">現在のユーザーをサインアウトして削除するには cookie 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-171">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="155e3-172">`CookieAuthenticationDefaults.AuthenticationScheme`(または " Cookie s") がスキームとして使用されていない場合 ("Contoso Cookie " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="155e3-172">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="155e3-173">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="155e3-174">ブラウザーが閉じると、セッションベースのが自動的に削除 cookie され cookie ますが (非持続 s)、個々のタブが閉じられても、 cookie はクリアされません。</span><span class="sxs-lookup"><span data-stu-id="155e3-174">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="155e3-175">サーバーには、タブまたはブラウザーの終了イベントは通知されません。</span><span class="sxs-lookup"><span data-stu-id="155e3-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="155e3-176">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="155e3-176">React to back-end changes</span></span>

<span data-ttu-id="155e3-177">が作成されると、は cookie cookie id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="155e3-177">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="155e3-178">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="155e3-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="155e3-179">アプリの cookie 認証システムは、認証に基づいて要求を処理し続け cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-179">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="155e3-180">認証が有効である限り、ユーザーはアプリにサインインしたままに cookie なります。</span><span class="sxs-lookup"><span data-stu-id="155e3-180">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="155e3-181">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-181">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="155e3-182">すべての要求でを検証すると、 cookie アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-182">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="155e3-183">検証の方法 cookie の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="155e3-183">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="155e3-184">ユーザーの発行後にデータベースが変更されていない場合は、 cookie ユーザー cookie がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="155e3-184">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="155e3-185">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="155e3-186">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="155e3-187">値に基づいてデータベースが変更されたときにを無効にするには、 cookie データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し cookie `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-187">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="155e3-188">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="155e3-189">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-189">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="155e3-190">サービス登録時にイベントインスタンスを cookie メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="155e3-190">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="155e3-191">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="155e3-192">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="155e3-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="155e3-193">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="155e3-194">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="155e3-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="155e3-195">cookieすべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-195">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="155e3-196">永続的な cookie s</span><span class="sxs-lookup"><span data-stu-id="155e3-196">Persistent cookies</span></span>

<span data-ttu-id="155e3-197">cookieをブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="155e3-197">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="155e3-198">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="155e3-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="155e3-199">次のコードスニペットは、 cookie ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="155e3-199">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="155e3-200">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="155e3-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="155e3-201">ブラウザーを閉じている間にが cookie 期限切れになると、ブラウザーは再起動後にをクリアし cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-201">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="155e3-202"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="155e3-203">絶対 cookie 有効期限</span><span class="sxs-lookup"><span data-stu-id="155e3-203">Absolute cookie expiration</span></span>

<span data-ttu-id="155e3-204">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="155e3-205">永続的なを作成するには cookie 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-205">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="155e3-206">それ以外の場合、は cookie セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-206">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="155e3-207">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="155e3-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="155e3-208">次のコードスニペットでは、 cookie 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="155e3-208">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="155e3-209">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-209">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="155e3-210">ASP.NET Core Identity は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="155e3-210">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="155e3-211">ただし、を cookie 使用しないベースの認証プロバイダーを ASP.NET Core Identity 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="155e3-211">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="155e3-212">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="155e3-213">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="155e3-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="155e3-214">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="155e3-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="155e3-215">**電子メール** アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="155e3-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="155e3-216">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="155e3-217">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="155e3-218">構成</span><span class="sxs-lookup"><span data-stu-id="155e3-218">Configuration</span></span>

<span data-ttu-id="155e3-219">アプリで [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore Cookie のプロジェクトファイルにパッケージ参照を作成します。s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="155e3-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="155e3-220">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="155e3-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="155e3-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="155e3-222">`AuthenticationScheme` は、認証の複数のインスタンスがあり cookie 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="155e3-222">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="155e3-223">`AuthenticationScheme`を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 Cookie</span><span class="sxs-lookup"><span data-stu-id="155e3-223">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="155e3-224">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="155e3-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="155e3-225">アプリの認証方式は、アプリの認証スキームとは異なり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-225">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="155e3-226">cookieに認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-226">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="155e3-227">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="155e3-227">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="155e3-228">cookieサイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-228">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="155e3-229">詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-229">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="155e3-230">メソッドで、 `Startup.Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="155e3-231">`UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="155e3-232">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-232">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="155e3-233">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-233">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="155e3-234">Cookie ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="155e3-234">Cookie Policy Middleware</span></span>

<span data-ttu-id="155e3-235">ポリシー [ Cookie ミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)は cookie 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="155e3-235">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="155e3-236">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="155e3-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="155e3-237"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>ポリシーミドルウェアに提供されるを使用して、 Cookie cookie cookie cookie が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="155e3-237">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="155e3-238">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="155e3-238">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="155e3-239">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="155e3-240">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 cookie クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="155e3-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="155e3-241">Cookieのポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `Cookie.SameSite` `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="155e3-241">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="155e3-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="155e3-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="155e3-243">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="155e3-243">Cookie.SameSite</span></span> | <span data-ttu-id="155e3-244">結果 Cookie 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="155e3-244">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="155e3-245">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-245">SameSiteMode.None</span></span>     | <span data-ttu-id="155e3-246">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-246">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-247">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-248">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-249">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-249">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-250">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-251">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="155e3-252">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="155e3-253">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-253">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-254">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-255">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-256">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-257">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-258">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="155e3-259">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="155e3-260">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-260">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-261">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-262">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-263">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="155e3-264">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="155e3-265">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="155e3-266">認証を作成する cookie</span><span class="sxs-lookup"><span data-stu-id="155e3-266">Create an authentication cookie</span></span>

<span data-ttu-id="155e3-267">保持しているユーザー情報を作成するに cookie は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="155e3-267">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="155e3-268">ユーザー情報はにシリアル化され、に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-268">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="155e3-269">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="155e3-269">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="155e3-270">`SignInAsync` 暗号化されたを作成 cookie し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="155e3-270">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="155e3-271">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="155e3-272">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="155e3-273">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="155e3-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="155e3-274">サインアウト</span><span class="sxs-lookup"><span data-stu-id="155e3-274">Sign out</span></span>

<span data-ttu-id="155e3-275">現在のユーザーをサインアウトして削除するには cookie 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-275">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="155e3-276">`CookieAuthenticationDefaults.AuthenticationScheme`(または " Cookie s") がスキームとして使用されていない場合 ("Contoso Cookie " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="155e3-276">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="155e3-277">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="155e3-278">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="155e3-278">React to back-end changes</span></span>

<span data-ttu-id="155e3-279">が作成されると、は cookie cookie id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="155e3-279">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="155e3-280">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="155e3-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="155e3-281">アプリの cookie 認証システムは、認証に基づいて要求を処理し続け cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-281">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="155e3-282">認証が有効である限り、ユーザーはアプリにサインインしたままに cookie なります。</span><span class="sxs-lookup"><span data-stu-id="155e3-282">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="155e3-283">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-283">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="155e3-284">すべての要求でを検証すると、 cookie アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-284">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="155e3-285">検証の方法 cookie の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="155e3-285">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="155e3-286">ユーザーの発行後にデータベースが変更されていない場合は、 cookie ユーザー cookie がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="155e3-286">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="155e3-287">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="155e3-288">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="155e3-289">値に基づいてデータベースが変更されたときにを無効にするには、 cookie データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し cookie `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-289">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="155e3-290">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="155e3-291">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-291">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="155e3-292">サービス登録時にイベントインスタンスを cookie メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="155e3-292">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="155e3-293">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="155e3-294">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="155e3-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="155e3-295">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="155e3-296">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="155e3-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="155e3-297">cookieすべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-297">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="155e3-298">永続的な cookie s</span><span class="sxs-lookup"><span data-stu-id="155e3-298">Persistent cookies</span></span>

<span data-ttu-id="155e3-299">cookieをブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="155e3-299">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="155e3-300">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="155e3-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="155e3-301">次のコードスニペットは、 cookie ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="155e3-301">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="155e3-302">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="155e3-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="155e3-303">ブラウザーを閉じている間にが cookie 期限切れになると、ブラウザーは再起動後にをクリアし cookie ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-303">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="155e3-304"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="155e3-305">絶対 cookie 有効期限</span><span class="sxs-lookup"><span data-stu-id="155e3-305">Absolute cookie expiration</span></span>

<span data-ttu-id="155e3-306">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="155e3-307">永続的なを作成するには cookie 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-307">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="155e3-308">それ以外の場合、は cookie セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-308">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="155e3-309">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="155e3-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="155e3-310">次のコードスニペットでは、 cookie 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="155e3-310">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="155e3-311">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-311">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="155e3-312">その他の資料</span><span class="sxs-lookup"><span data-stu-id="155e3-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="155e3-313">ポリシーベースのロールチェック</span><span class="sxs-lookup"><span data-stu-id="155e3-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
