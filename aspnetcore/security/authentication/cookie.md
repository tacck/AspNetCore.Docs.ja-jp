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
ms.openlocfilehash: 2e9eb58837d74343d8de6903372146570b43f330
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627144"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="f4a5f-103">認証を使用 cookie しない ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f4a5f-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="f4a5f-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f4a5f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4a5f-105">ASP.NET Core Identity は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="f4a5f-106">ただし、を cookie 使用しないベースの認証プロバイダーを ASP.NET Core Identity 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="f4a5f-107">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="f4a5f-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f4a5f-109">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="f4a5f-110">**電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="f4a5f-111">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="f4a5f-112">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="f4a5f-113">構成</span><span class="sxs-lookup"><span data-stu-id="f4a5f-113">Configuration</span></span>

<span data-ttu-id="f4a5f-114">アプリで [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore Cookie のプロジェクトファイルにパッケージ参照を作成します。s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="f4a5f-115">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="f4a5f-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="f4a5f-117">`AuthenticationScheme` は、認証の複数のインスタンスがあり cookie 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="f4a5f-118">`AuthenticationScheme`を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 Cookie</span><span class="sxs-lookup"><span data-stu-id="f4a5f-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="f4a5f-119">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="f4a5f-120">アプリの認証方式は、アプリの認証スキームとは異なり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="f4a5f-121">cookieに認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="f4a5f-122">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="f4a5f-123">cookieサイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="f4a5f-124">詳細については、<xref:security/gdpr#essential-cookies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="f4a5f-125">で、とを呼び出して、 `Startup.Configure` `UseAuthentication` プロパティを `UseAuthorization` 設定し、 `HttpContext.User` 要求の承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="f4a5f-126">`UseAuthentication`を `UseAuthorization` 呼び出す前に、メソッドとメソッドを呼び出し `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="f4a5f-127">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="f4a5f-128">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="f4a5f-129">Cookie ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f4a5f-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="f4a5f-130">ポリシー [ Cookie ミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)は cookie 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="f4a5f-131">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="f4a5f-132"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>ポリシーミドルウェアに提供されるを使用して、 Cookie cookie cookie cookie が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="f4a5f-133">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="f4a5f-134">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="f4a5f-135">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 cookie クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="f4a5f-136">Cookieのポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `Cookie.SameSite` `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="f4a5f-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="f4a5f-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="f4a5f-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="f4a5f-138">Cookie.SameSite</span></span> | <span data-ttu-id="f4a5f-139">結果 Cookie 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="f4a5f-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="f4a5f-140">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-140">SameSiteMode.None</span></span>     | <span data-ttu-id="f4a5f-141">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-141">SameSiteMode.None</span></span><br><span data-ttu-id="f4a5f-142">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-143">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="f4a5f-144">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-144">SameSiteMode.None</span></span><br><span data-ttu-id="f4a5f-145">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-146">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f4a5f-147">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="f4a5f-148">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-148">SameSiteMode.None</span></span><br><span data-ttu-id="f4a5f-149">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-150">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="f4a5f-151">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-152">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-153">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f4a5f-154">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="f4a5f-155">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-155">SameSiteMode.None</span></span><br><span data-ttu-id="f4a5f-156">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-157">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="f4a5f-158">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="f4a5f-159">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="f4a5f-160">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="f4a5f-161">認証を作成する cookie</span><span class="sxs-lookup"><span data-stu-id="f4a5f-161">Create an authentication cookie</span></span>

<span data-ttu-id="f4a5f-162">保持しているユーザー情報を作成するに cookie は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="f4a5f-163">ユーザー情報はにシリアル化され、に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="f4a5f-164">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="f4a5f-165">`SignInAsync` 暗号化されたを作成 cookie し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="f4a5f-166">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="f4a5f-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> は、既定ではいくつかの特定のパス (ログインパスやログアウトパスなど) でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="f4a5f-168">詳細については、「 [ Cookie authenticationhandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="f4a5f-169">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="f4a5f-170">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="f4a5f-171">サインアウト</span><span class="sxs-lookup"><span data-stu-id="f4a5f-171">Sign out</span></span>

<span data-ttu-id="f4a5f-172">現在のユーザーをサインアウトして削除するには cookie 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="f4a5f-173">`CookieAuthenticationDefaults.AuthenticationScheme`(または " Cookie s") がスキームとして使用されていない場合 ("Contoso Cookie " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="f4a5f-174">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="f4a5f-175">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="f4a5f-175">React to back-end changes</span></span>

<span data-ttu-id="f4a5f-176">が作成されると、は cookie cookie id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="f4a5f-177">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="f4a5f-178">アプリの cookie 認証システムは、認証に基づいて要求を処理し続け cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="f4a5f-179">認証が有効である限り、ユーザーはアプリにサインインしたままに cookie なります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="f4a5f-180">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="f4a5f-181">すべての要求でを検証すると、 cookie アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="f4a5f-182">検証の方法 cookie の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="f4a5f-183">ユーザーの発行後にデータベースが変更されていない場合は、 cookie ユーザー cookie がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="f4a5f-184">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="f4a5f-185">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="f4a5f-186">値に基づいてデータベースが変更されたときにを無効にするには、 cookie データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し cookie `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="f4a5f-187">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="f4a5f-188">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="f4a5f-189">サービス登録時にイベントインスタンスを cookie メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f4a5f-190">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="f4a5f-191">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="f4a5f-192">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f4a5f-193">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="f4a5f-194">cookieすべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="f4a5f-195">永続的な cookie s</span><span class="sxs-lookup"><span data-stu-id="f4a5f-195">Persistent cookies</span></span>

<span data-ttu-id="f4a5f-196">cookieをブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="f4a5f-197">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="f4a5f-198">次のコードスニペットは、 cookie ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="f4a5f-199">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="f4a5f-200">ブラウザーを閉じている間にが cookie 期限切れになると、ブラウザーは再起動後にをクリアし cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="f4a5f-201"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="f4a5f-202">絶対 cookie 有効期限</span><span class="sxs-lookup"><span data-stu-id="f4a5f-202">Absolute cookie expiration</span></span>

<span data-ttu-id="f4a5f-203">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="f4a5f-204">永続的なを作成するには cookie 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="f4a5f-205">それ以外の場合、は cookie セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="f4a5f-206">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="f4a5f-207">次のコードスニペットでは、 cookie 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="f4a5f-208">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="f4a5f-209">ASP.NET Core Identity は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="f4a5f-210">ただし、を cookie 使用しないベースの認証認証プロバイダーを ASP.NET Core Identity 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="f4a5f-211">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="f4a5f-212">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f4a5f-213">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="f4a5f-214">**電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="f4a5f-215">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="f4a5f-216">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="f4a5f-217">構成</span><span class="sxs-lookup"><span data-stu-id="f4a5f-217">Configuration</span></span>

<span data-ttu-id="f4a5f-218">アプリで [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore Cookie のプロジェクトファイルにパッケージ参照を作成します。s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="f4a5f-219">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="f4a5f-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="f4a5f-221">`AuthenticationScheme` は、認証の複数のインスタンスがあり cookie 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="f4a5f-222">`AuthenticationScheme`を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 Cookie</span><span class="sxs-lookup"><span data-stu-id="f4a5f-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="f4a5f-223">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="f4a5f-224">アプリの認証方式は、アプリの認証スキームとは異なり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="f4a5f-225">cookieに認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="f4a5f-226">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="f4a5f-227">cookieサイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="f4a5f-228">詳細については、<xref:security/gdpr#essential-cookies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="f4a5f-229">メソッドで、 `Startup.Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="f4a5f-230">`UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="f4a5f-231">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="f4a5f-232">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="f4a5f-233">Cookie ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f4a5f-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="f4a5f-234">ポリシー [ Cookie ミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)は cookie 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="f4a5f-235">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="f4a5f-236"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>ポリシーミドルウェアに提供されるを使用して、 Cookie cookie cookie cookie が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="f4a5f-237">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="f4a5f-238">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="f4a5f-239">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 cookie クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="f4a5f-240">Cookieのポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `Cookie.SameSite` `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="f4a5f-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="f4a5f-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="f4a5f-242">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="f4a5f-242">Cookie.SameSite</span></span> | <span data-ttu-id="f4a5f-243">結果 Cookie 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="f4a5f-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="f4a5f-244">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-244">SameSiteMode.None</span></span>     | <span data-ttu-id="f4a5f-245">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-245">SameSiteMode.None</span></span><br><span data-ttu-id="f4a5f-246">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-247">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="f4a5f-248">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-248">SameSiteMode.None</span></span><br><span data-ttu-id="f4a5f-249">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-250">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f4a5f-251">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="f4a5f-252">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-252">SameSiteMode.None</span></span><br><span data-ttu-id="f4a5f-253">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-254">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="f4a5f-255">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-256">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-257">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f4a5f-258">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="f4a5f-259">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="f4a5f-259">SameSiteMode.None</span></span><br><span data-ttu-id="f4a5f-260">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="f4a5f-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="f4a5f-261">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="f4a5f-262">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="f4a5f-263">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="f4a5f-264">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="f4a5f-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="f4a5f-265">認証を作成する cookie</span><span class="sxs-lookup"><span data-stu-id="f4a5f-265">Create an authentication cookie</span></span>

<span data-ttu-id="f4a5f-266">保持しているユーザー情報を作成するに cookie は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="f4a5f-267">ユーザー情報はにシリアル化され、に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="f4a5f-268">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="f4a5f-269">`SignInAsync` 暗号化されたを作成 cookie し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="f4a5f-270">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="f4a5f-271">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="f4a5f-272">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="f4a5f-273">サインアウト</span><span class="sxs-lookup"><span data-stu-id="f4a5f-273">Sign out</span></span>

<span data-ttu-id="f4a5f-274">現在のユーザーをサインアウトして削除するには cookie 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="f4a5f-275">`CookieAuthenticationDefaults.AuthenticationScheme`(または " Cookie s") がスキームとして使用されていない場合 ("Contoso Cookie " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="f4a5f-276">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="f4a5f-277">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="f4a5f-277">React to back-end changes</span></span>

<span data-ttu-id="f4a5f-278">が作成されると、は cookie cookie id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="f4a5f-279">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="f4a5f-280">アプリの cookie 認証システムは、認証に基づいて要求を処理し続け cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="f4a5f-281">認証が有効である限り、ユーザーはアプリにサインインしたままに cookie なります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="f4a5f-282">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="f4a5f-283">すべての要求でを検証すると、 cookie アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="f4a5f-284">検証の方法 cookie の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="f4a5f-285">ユーザーの発行後にデータベースが変更されていない場合は、 cookie ユーザー cookie がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="f4a5f-286">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="f4a5f-287">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="f4a5f-288">値に基づいてデータベースが変更されたときにを無効にするには、 cookie データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し cookie `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="f4a5f-289">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="f4a5f-290">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="f4a5f-291">サービス登録時にイベントインスタンスを cookie メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f4a5f-292">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="f4a5f-293">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="f4a5f-294">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f4a5f-295">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="f4a5f-296">cookieすべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="f4a5f-297">永続的な cookie s</span><span class="sxs-lookup"><span data-stu-id="f4a5f-297">Persistent cookies</span></span>

<span data-ttu-id="f4a5f-298">cookieをブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="f4a5f-299">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="f4a5f-300">次のコードスニペットは、 cookie ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="f4a5f-301">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="f4a5f-302">ブラウザーを閉じている間にが cookie 期限切れになると、ブラウザーは再起動後にをクリアし cookie ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="f4a5f-303"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="f4a5f-304">絶対 cookie 有効期限</span><span class="sxs-lookup"><span data-stu-id="f4a5f-304">Absolute cookie expiration</span></span>

<span data-ttu-id="f4a5f-305">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="f4a5f-306">永続的なを作成するには cookie 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="f4a5f-307">それ以外の場合、は cookie セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="f4a5f-308">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="f4a5f-309">次のコードスニペットでは、 cookie 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="f4a5f-310">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="f4a5f-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f4a5f-311">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="f4a5f-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="f4a5f-312">ポリシーベースのロールチェック</span><span class="sxs-lookup"><span data-stu-id="f4a5f-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
