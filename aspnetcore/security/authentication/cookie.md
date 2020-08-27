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
ms.openlocfilehash: 24ba49828db08fdd67723c81ac0c8d9981ab3404
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945416"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="e6c36-103">認証を使用 cookie しない ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="e6c36-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="e6c36-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e6c36-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6c36-105">ASP.NET Core Identity は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="e6c36-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e6c36-106">ただし、を cookie 使用しないベースの認証プロバイダーを ASP.NET Core Identity 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="e6c36-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e6c36-107">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6c36-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e6c36-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e6c36-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e6c36-109">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="e6c36-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e6c36-110">**電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="e6c36-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e6c36-111">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e6c36-112">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e6c36-113">構成</span><span class="sxs-lookup"><span data-stu-id="e6c36-113">Configuration</span></span>

<span data-ttu-id="e6c36-114">アプリで [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore Cookie のプロジェクトファイルにパッケージ参照を作成します。s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="e6c36-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e6c36-115">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e6c36-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e6c36-117">`AuthenticationScheme` は、認証の複数のインスタンスがあり cookie 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="e6c36-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e6c36-118">`AuthenticationScheme`を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 Cookie</span><span class="sxs-lookup"><span data-stu-id="e6c36-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e6c36-119">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e6c36-120">アプリの認証方式は、アプリの認証スキームとは異なり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e6c36-121">cookieに認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e6c36-122">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="e6c36-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e6c36-123">cookieサイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e6c36-124">詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6c36-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e6c36-125">で、とを呼び出して、 `Startup.Configure` `UseAuthentication` プロパティを `UseAuthorization` 設定し、 `HttpContext.User` 要求の承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="e6c36-126">`UseAuthentication`を `UseAuthorization` 呼び出す前に、メソッドとメソッドを呼び出し `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e6c36-127">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e6c36-128">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e6c36-129">Cookie ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="e6c36-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="e6c36-130">ポリシー [ Cookie ミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)は cookie 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e6c36-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e6c36-131">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e6c36-132"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>ポリシーミドルウェアに提供されるを使用して、 Cookie cookie cookie cookie が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e6c36-133">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="e6c36-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e6c36-134">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e6c36-135">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 cookie クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e6c36-136">Cookieのポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `Cookie.SameSite` `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e6c36-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e6c36-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e6c36-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="e6c36-138">Cookie.SameSite</span></span> | <span data-ttu-id="e6c36-139">結果 Cookie 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="e6c36-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e6c36-140">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-140">SameSiteMode.None</span></span>     | <span data-ttu-id="e6c36-141">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-141">SameSiteMode.None</span></span><br><span data-ttu-id="e6c36-142">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-143">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="e6c36-144">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-144">SameSiteMode.None</span></span><br><span data-ttu-id="e6c36-145">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-146">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e6c36-147">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e6c36-148">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-148">SameSiteMode.None</span></span><br><span data-ttu-id="e6c36-149">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-150">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="e6c36-151">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-152">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-153">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e6c36-154">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e6c36-155">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-155">SameSiteMode.None</span></span><br><span data-ttu-id="e6c36-156">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-157">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="e6c36-158">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="e6c36-159">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="e6c36-160">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e6c36-161">認証を作成する cookie</span><span class="sxs-lookup"><span data-stu-id="e6c36-161">Create an authentication cookie</span></span>

<span data-ttu-id="e6c36-162">保持しているユーザー情報を作成するに cookie は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e6c36-163">ユーザー情報はにシリアル化され、に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e6c36-164">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="e6c36-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e6c36-165">`SignInAsync` 暗号化されたを作成 cookie し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e6c36-166">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e6c36-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> は、既定ではいくつかの特定のパス (ログインパスやログアウトパスなど) でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="e6c36-168">詳細については、「 [ Cookie authenticationhandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6c36-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="e6c36-169">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e6c36-170">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e6c36-171">サインアウト</span><span class="sxs-lookup"><span data-stu-id="e6c36-171">Sign out</span></span>

<span data-ttu-id="e6c36-172">現在のユーザーをサインアウトして削除するには cookie 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e6c36-173">`CookieAuthenticationDefaults.AuthenticationScheme`(または " Cookie s") がスキームとして使用されていない場合 ("Contoso Cookie " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e6c36-174">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-174">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="e6c36-175">ブラウザーが閉じると、セッションベースのが自動的に削除 cookie され cookie ますが (非持続 s)、個々のタブが閉じられても、 cookie はクリアされません。</span><span class="sxs-lookup"><span data-stu-id="e6c36-175">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="e6c36-176">サーバーには、タブまたはブラウザーの終了イベントは通知されません。</span><span class="sxs-lookup"><span data-stu-id="e6c36-176">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e6c36-177">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="e6c36-177">React to back-end changes</span></span>

<span data-ttu-id="e6c36-178">が作成されると、は cookie cookie id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-178">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e6c36-179">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-179">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e6c36-180">アプリの cookie 認証システムは、認証に基づいて要求を処理し続け cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-180">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e6c36-181">認証が有効である限り、ユーザーはアプリにサインインしたままに cookie なります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-181">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e6c36-182">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-182">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e6c36-183">すべての要求でを検証すると、 cookie アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-183">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e6c36-184">検証の方法 cookie の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="e6c36-184">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e6c36-185">ユーザーの発行後にデータベースが変更されていない場合は、 cookie ユーザー cookie がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e6c36-185">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e6c36-186">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-186">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e6c36-187">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-187">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e6c36-188">値に基づいてデータベースが変更されたときにを無効にするには、 cookie データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し cookie `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-188">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e6c36-189">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-189">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e6c36-190">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-190">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e6c36-191">サービス登録時にイベントインスタンスを cookie メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-191">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e6c36-192">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-192">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e6c36-193">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-193">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e6c36-194">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-194">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e6c36-195">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-195">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e6c36-196">cookieすべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-196">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e6c36-197">永続的な cookie s</span><span class="sxs-lookup"><span data-stu-id="e6c36-197">Persistent cookies</span></span>

<span data-ttu-id="e6c36-198">cookieをブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e6c36-198">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e6c36-199">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="e6c36-199">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e6c36-200">次のコードスニペットは、 cookie ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-200">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e6c36-201">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-201">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e6c36-202">ブラウザーを閉じている間にが cookie 期限切れになると、ブラウザーは再起動後にをクリアし cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-202">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e6c36-203"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-203">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e6c36-204">絶対 cookie 有効期限</span><span class="sxs-lookup"><span data-stu-id="e6c36-204">Absolute cookie expiration</span></span>

<span data-ttu-id="e6c36-205">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-205">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e6c36-206">永続的なを作成するには cookie 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-206">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e6c36-207">それ以外の場合、は cookie セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-207">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e6c36-208">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="e6c36-208">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e6c36-209">次のコードスニペットでは、 cookie 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-209">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e6c36-210">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-210">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="e6c36-211">ASP.NET Core Identity は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="e6c36-211">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e6c36-212">ただし、を cookie 使用しないベースの認証プロバイダーを ASP.NET Core Identity 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="e6c36-212">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e6c36-213">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6c36-213">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e6c36-214">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e6c36-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e6c36-215">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="e6c36-215">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e6c36-216">**電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="e6c36-216">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e6c36-217">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-217">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e6c36-218">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-218">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e6c36-219">構成</span><span class="sxs-lookup"><span data-stu-id="e6c36-219">Configuration</span></span>

<span data-ttu-id="e6c36-220">アプリで [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore Cookie のプロジェクトファイルにパッケージ参照を作成します。s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="e6c36-220">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e6c36-221">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-221">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e6c36-222"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-222"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e6c36-223">`AuthenticationScheme` は、認証の複数のインスタンスがあり cookie 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="e6c36-223">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e6c36-224">`AuthenticationScheme`を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 Cookie</span><span class="sxs-lookup"><span data-stu-id="e6c36-224">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e6c36-225">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-225">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e6c36-226">アプリの認証方式は、アプリの認証スキームとは異なり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-226">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e6c36-227">cookieに認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-227">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e6c36-228">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="e6c36-228">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e6c36-229">cookieサイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-229">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e6c36-230">詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6c36-230">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e6c36-231">メソッドで、 `Startup.Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-231">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="e6c36-232">`UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-232">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e6c36-233">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-233">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e6c36-234">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-234">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e6c36-235">Cookie ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="e6c36-235">Cookie Policy Middleware</span></span>

<span data-ttu-id="e6c36-236">ポリシー [ Cookie ミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)は cookie 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e6c36-236">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e6c36-237">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-237">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e6c36-238"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>ポリシーミドルウェアに提供されるを使用して、 Cookie cookie cookie cookie が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-238">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e6c36-239">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="e6c36-239">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e6c36-240">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-240">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e6c36-241">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 cookie クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-241">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e6c36-242">Cookieのポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `Cookie.SameSite` `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-242">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e6c36-243">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e6c36-243">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e6c36-244">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="e6c36-244">Cookie.SameSite</span></span> | <span data-ttu-id="e6c36-245">結果 Cookie 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="e6c36-245">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e6c36-246">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-246">SameSiteMode.None</span></span>     | <span data-ttu-id="e6c36-247">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-247">SameSiteMode.None</span></span><br><span data-ttu-id="e6c36-248">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-248">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-249">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-249">SameSiteMode.Strict</span></span> | <span data-ttu-id="e6c36-250">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-250">SameSiteMode.None</span></span><br><span data-ttu-id="e6c36-251">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-252">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-252">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e6c36-253">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-253">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e6c36-254">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-254">SameSiteMode.None</span></span><br><span data-ttu-id="e6c36-255">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-256">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-256">SameSiteMode.Strict</span></span> | <span data-ttu-id="e6c36-257">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-258">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-259">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-259">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e6c36-260">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-260">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e6c36-261">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="e6c36-261">SameSiteMode.None</span></span><br><span data-ttu-id="e6c36-262">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="e6c36-262">SameSiteMode.Lax</span></span><br><span data-ttu-id="e6c36-263">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-263">SameSiteMode.Strict</span></span> | <span data-ttu-id="e6c36-264">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="e6c36-265">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-265">SameSiteMode.Strict</span></span><br><span data-ttu-id="e6c36-266">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="e6c36-266">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e6c36-267">認証を作成する cookie</span><span class="sxs-lookup"><span data-stu-id="e6c36-267">Create an authentication cookie</span></span>

<span data-ttu-id="e6c36-268">保持しているユーザー情報を作成するに cookie は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-268">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e6c36-269">ユーザー情報はにシリアル化され、に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-269">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e6c36-270">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="e6c36-270">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="e6c36-271">`SignInAsync` 暗号化されたを作成 cookie し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-271">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e6c36-272">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-272">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e6c36-273">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-273">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e6c36-274">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-274">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e6c36-275">サインアウト</span><span class="sxs-lookup"><span data-stu-id="e6c36-275">Sign out</span></span>

<span data-ttu-id="e6c36-276">現在のユーザーをサインアウトして削除するには cookie 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-276">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e6c36-277">`CookieAuthenticationDefaults.AuthenticationScheme`(または " Cookie s") がスキームとして使用されていない場合 ("Contoso Cookie " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-277">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e6c36-278">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-278">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e6c36-279">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="e6c36-279">React to back-end changes</span></span>

<span data-ttu-id="e6c36-280">が作成されると、は cookie cookie id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-280">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e6c36-281">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-281">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e6c36-282">アプリの cookie 認証システムは、認証に基づいて要求を処理し続け cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-282">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e6c36-283">認証が有効である限り、ユーザーはアプリにサインインしたままに cookie なります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-283">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e6c36-284">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-284">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e6c36-285">すべての要求でを検証すると、 cookie アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-285">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e6c36-286">検証の方法 cookie の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="e6c36-286">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e6c36-287">ユーザーの発行後にデータベースが変更されていない場合は、 cookie ユーザー cookie がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e6c36-287">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e6c36-288">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-288">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e6c36-289">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-289">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e6c36-290">値に基づいてデータベースが変更されたときにを無効にするには、 cookie データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し cookie `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-290">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e6c36-291">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-291">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e6c36-292">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-292">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e6c36-293">サービス登録時にイベントインスタンスを cookie メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-293">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e6c36-294">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-294">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e6c36-295">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-295">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e6c36-296">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-296">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e6c36-297">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-297">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e6c36-298">cookieすべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-298">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e6c36-299">永続的な cookie s</span><span class="sxs-lookup"><span data-stu-id="e6c36-299">Persistent cookies</span></span>

<span data-ttu-id="e6c36-300">cookieをブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e6c36-300">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e6c36-301">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="e6c36-301">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e6c36-302">次のコードスニペットは、 cookie ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-302">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e6c36-303">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-303">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e6c36-304">ブラウザーを閉じている間にが cookie 期限切れになると、ブラウザーは再起動後にをクリアし cookie ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-304">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e6c36-305"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-305">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e6c36-306">絶対 cookie 有効期限</span><span class="sxs-lookup"><span data-stu-id="e6c36-306">Absolute cookie expiration</span></span>

<span data-ttu-id="e6c36-307">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-307">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e6c36-308">永続的なを作成するには cookie 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-308">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e6c36-309">それ以外の場合、は cookie セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e6c36-309">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e6c36-310">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="e6c36-310">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e6c36-311">次のコードスニペットでは、 cookie 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="e6c36-311">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e6c36-312">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="e6c36-312">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e6c36-313">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e6c36-313">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="e6c36-314">ポリシーベースのロールチェック</span><span class="sxs-lookup"><span data-stu-id="e6c36-314">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
