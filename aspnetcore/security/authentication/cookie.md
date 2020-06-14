---
title: ASP.NET Core せずに cookie 認証を使用するIdentity
author: rick-anderson
description: ASP.NET Core せずに cookie 認証を使用する方法について説明 Identity します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 7d2f338f8ece6bd3cc99d5f2ab8153b5c465c7a4
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724238"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="13a8e-103">ASP.NET Core せずに cookie 認証を使用するIdentity</span><span class="sxs-lookup"><span data-stu-id="13a8e-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="13a8e-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="13a8e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="13a8e-105">ASP.NET Core Identity は、ログインを作成および管理するためのフル機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="13a8e-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="13a8e-106">ただし、ASP.NET Core のない cookie ベースの認証プロバイダーを Identity 使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="13a8e-107">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13a8e-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="13a8e-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="13a8e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="13a8e-109">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="13a8e-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="13a8e-110">**電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="13a8e-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="13a8e-111">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs*ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="13a8e-112">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="13a8e-113">構成</span><span class="sxs-lookup"><span data-stu-id="13a8e-113">Configuration</span></span>

<span data-ttu-id="13a8e-114">アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="13a8e-115">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="13a8e-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="13a8e-117">`AuthenticationScheme`は、cookie 認証のインスタンスが複数存在し、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="13a8e-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="13a8e-118">`AuthenticationScheme`を[Cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="13a8e-119">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="13a8e-120">アプリの認証方式は、アプリの cookie 認証スキームとは異なります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="13a8e-121">クッキー認証スキームがに指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (cookie) を使用します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="13a8e-122">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="13a8e-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="13a8e-123">サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="13a8e-124">詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13a8e-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="13a8e-125">で、とを呼び出して、 `Startup.Configure` `UseAuthentication` プロパティを `UseAuthorization` 設定し、 `HttpContext.User` 要求の承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="13a8e-126">`UseAuthentication`を `UseAuthorization` 呼び出す前に、メソッドとメソッドを呼び出し `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="13a8e-127">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="13a8e-128">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="13a8e-129">クッキーポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="13a8e-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="13a8e-130">Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="13a8e-131">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="13a8e-132">Cookie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするために、クッキーポリシーミドルウェアに提供されているを使用します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="13a8e-133">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="13a8e-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="13a8e-134">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="13a8e-135">この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="13a8e-136">の Cookie ポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` `Cookie.SameSite` に従って設定内のの設定に影響を与える可能性が `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="13a8e-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="13a8e-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="13a8e-138">SameSite</span><span class="sxs-lookup"><span data-stu-id="13a8e-138">Cookie.SameSite</span></span> | <span data-ttu-id="13a8e-139">結果の SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="13a8e-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="13a8e-140">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-140">SameSiteMode.None</span></span>     | <span data-ttu-id="13a8e-141">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-141">SameSiteMode.None</span></span><br><span data-ttu-id="13a8e-142">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-143">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="13a8e-144">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-144">SameSiteMode.None</span></span><br><span data-ttu-id="13a8e-145">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-146">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="13a8e-147">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="13a8e-148">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-148">SameSiteMode.None</span></span><br><span data-ttu-id="13a8e-149">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-150">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="13a8e-151">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-152">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-153">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="13a8e-154">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="13a8e-155">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-155">SameSiteMode.None</span></span><br><span data-ttu-id="13a8e-156">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-157">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="13a8e-158">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="13a8e-159">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="13a8e-160">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="13a8e-161">認証 cookie を作成する</span><span class="sxs-lookup"><span data-stu-id="13a8e-161">Create an authentication cookie</span></span>

<span data-ttu-id="13a8e-162">ユーザー情報を保持する cookie を作成するには、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="13a8e-163">ユーザー情報がシリアル化され、cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="13a8e-164">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="13a8e-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="13a8e-165">`SignInAsync`暗号化されたクッキーを作成し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="13a8e-166">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="13a8e-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>は、既定ではいくつかの特定のパス (ログインパスやログアウトパスなど) でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="13a8e-168">詳細については、「 [Cookieauthenticationhandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13a8e-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="13a8e-169">暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="13a8e-170">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="13a8e-171">サインアウト</span><span class="sxs-lookup"><span data-stu-id="13a8e-171">Sign out</span></span>

<span data-ttu-id="13a8e-172">現在のユーザーをサインアウトして cookie を削除するには、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="13a8e-173">`CookieAuthenticationDefaults.AuthenticationScheme`(または "cookie") がスキームとして使用されていない場合 (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="13a8e-174">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="13a8e-175">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="13a8e-175">React to back-end changes</span></span>

<span data-ttu-id="13a8e-176">Cookie が作成されると、cookie は id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="13a8e-177">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="13a8e-178">アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="13a8e-179">認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="13a8e-180"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="13a8e-181">すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="13a8e-182">Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="13a8e-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="13a8e-183">ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="13a8e-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="13a8e-184">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="13a8e-185">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="13a8e-186">データベースが値に基づいて変更されたときに cookie を無効にするには `LastChanged` 、 `LastChanged` データベースから現在の値を含むクレームを使用して cookie を作成し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="13a8e-187">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="13a8e-188">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="13a8e-189">Cookie サービスの登録時に、メソッドでイベントインスタンスを登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="13a8e-190">クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="13a8e-191">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="13a8e-192">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="13a8e-193">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="13a8e-194">すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="13a8e-195">永続的な cookie</span><span class="sxs-lookup"><span data-stu-id="13a8e-195">Persistent cookies</span></span>

<span data-ttu-id="13a8e-196">Cookie がブラウザーセッション間で保持されるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="13a8e-197">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="13a8e-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="13a8e-198">次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="13a8e-199">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="13a8e-200">ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。</span><span class="sxs-lookup"><span data-stu-id="13a8e-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="13a8e-201"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="13a8e-202">クッキーの絶対有効期限</span><span class="sxs-lookup"><span data-stu-id="13a8e-202">Absolute cookie expiration</span></span>

<span data-ttu-id="13a8e-203">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="13a8e-204">永続的なクッキーを作成するには、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="13a8e-205">それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="13a8e-206">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="13a8e-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="13a8e-207">次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="13a8e-208">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="13a8e-209">ASP.NET Core Identity は、ログインを作成および管理するためのフル機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="13a8e-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="13a8e-210">ただし、ASP.NET Core のない cookie ベースの認証認証プロバイダーを Identity 使用できます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="13a8e-211">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13a8e-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="13a8e-212">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="13a8e-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="13a8e-213">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="13a8e-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="13a8e-214">**電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="13a8e-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="13a8e-215">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs*ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="13a8e-216">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="13a8e-217">構成</span><span class="sxs-lookup"><span data-stu-id="13a8e-217">Configuration</span></span>

<span data-ttu-id="13a8e-218">アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="13a8e-219">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="13a8e-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="13a8e-221">`AuthenticationScheme`は、cookie 認証のインスタンスが複数存在し、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="13a8e-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="13a8e-222">`AuthenticationScheme`を[Cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="13a8e-223">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="13a8e-224">アプリの認証方式は、アプリの cookie 認証スキームとは異なります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="13a8e-225">クッキー認証スキームがに指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (cookie) を使用します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="13a8e-226">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="13a8e-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="13a8e-227">サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="13a8e-228">詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13a8e-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="13a8e-229">メソッドで、 `Startup.Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="13a8e-230">`UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="13a8e-231">クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="13a8e-232">`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="13a8e-233">クッキーポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="13a8e-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="13a8e-234">Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="13a8e-235">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="13a8e-236">Cookie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするために、クッキーポリシーミドルウェアに提供されているを使用します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="13a8e-237">既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="13a8e-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="13a8e-238">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="13a8e-239">この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="13a8e-240">の Cookie ポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` `Cookie.SameSite` に従って設定内のの設定に影響を与える可能性が `CookieAuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="13a8e-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="13a8e-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="13a8e-242">SameSite</span><span class="sxs-lookup"><span data-stu-id="13a8e-242">Cookie.SameSite</span></span> | <span data-ttu-id="13a8e-243">結果の SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="13a8e-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="13a8e-244">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-244">SameSiteMode.None</span></span>     | <span data-ttu-id="13a8e-245">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-245">SameSiteMode.None</span></span><br><span data-ttu-id="13a8e-246">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-247">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="13a8e-248">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-248">SameSiteMode.None</span></span><br><span data-ttu-id="13a8e-249">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-250">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="13a8e-251">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="13a8e-252">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-252">SameSiteMode.None</span></span><br><span data-ttu-id="13a8e-253">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-254">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="13a8e-255">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-256">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-257">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="13a8e-258">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="13a8e-259">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="13a8e-259">SameSiteMode.None</span></span><br><span data-ttu-id="13a8e-260">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="13a8e-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="13a8e-261">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="13a8e-262">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="13a8e-263">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="13a8e-264">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="13a8e-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="13a8e-265">認証 cookie を作成する</span><span class="sxs-lookup"><span data-stu-id="13a8e-265">Create an authentication cookie</span></span>

<span data-ttu-id="13a8e-266">ユーザー情報を保持する cookie を作成するには、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="13a8e-267">ユーザー情報がシリアル化され、cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="13a8e-268">任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="13a8e-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="13a8e-269">`SignInAsync`暗号化されたクッキーを作成し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="13a8e-270">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="13a8e-271">暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="13a8e-272">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="13a8e-273">サインアウト</span><span class="sxs-lookup"><span data-stu-id="13a8e-273">Sign out</span></span>

<span data-ttu-id="13a8e-274">現在のユーザーをサインアウトして cookie を削除するには、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="13a8e-275">`CookieAuthenticationDefaults.AuthenticationScheme`(または "cookie") がスキームとして使用されていない場合 (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="13a8e-276">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="13a8e-277">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="13a8e-277">React to back-end changes</span></span>

<span data-ttu-id="13a8e-278">Cookie が作成されると、cookie は id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="13a8e-279">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="13a8e-280">アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="13a8e-281">認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="13a8e-282"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="13a8e-283">すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="13a8e-284">Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="13a8e-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="13a8e-285">ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="13a8e-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="13a8e-286">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="13a8e-287">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="13a8e-288">データベースが値に基づいて変更されたときに cookie を無効にするには `LastChanged` 、 `LastChanged` データベースから現在の値を含むクレームを使用して cookie を作成し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="13a8e-289">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="13a8e-290">の実装例を次に示し `CookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="13a8e-291">Cookie サービスの登録時に、メソッドでイベントインスタンスを登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="13a8e-292">クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定し `CustomCookieAuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="13a8e-293">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="13a8e-294">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="13a8e-295">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="13a8e-296">すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="13a8e-297">永続的な cookie</span><span class="sxs-lookup"><span data-stu-id="13a8e-297">Persistent cookies</span></span>

<span data-ttu-id="13a8e-298">Cookie がブラウザーセッション間で保持されるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="13a8e-299">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="13a8e-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="13a8e-300">次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="13a8e-301">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="13a8e-302">ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。</span><span class="sxs-lookup"><span data-stu-id="13a8e-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="13a8e-303"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="13a8e-304">クッキーの絶対有効期限</span><span class="sxs-lookup"><span data-stu-id="13a8e-304">Absolute cookie expiration</span></span>

<span data-ttu-id="13a8e-305">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="13a8e-306">永続的なクッキーを作成するには、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="13a8e-307">それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13a8e-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="13a8e-308">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="13a8e-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="13a8e-309">次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="13a8e-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="13a8e-310">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="13a8e-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="13a8e-311">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="13a8e-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="13a8e-312">ポリシーベースのロールチェック</span><span class="sxs-lookup"><span data-stu-id="13a8e-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
