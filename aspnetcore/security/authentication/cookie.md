---
title: ASP.NET Core せずに cookie 認証を使用するIdentity
author: rick-anderson
description: ASP.NET Core Identityせずに cookie 認証を使用する方法について説明します。
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
ms.openlocfilehash: c179b3657ad4cbda960c2afe685a63f3266a7402
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773845"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="8a0d1-103">ASP.NET Core Id を指定せずに cookie 認証を使用する</span><span class="sxs-lookup"><span data-stu-id="8a0d1-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="8a0d1-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8a0d1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a0d1-105">ASP.NET Core Id は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="8a0d1-106">ただし、ASP.NET Core Id のない cookie ベースの認証プロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="8a0d1-107">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="8a0d1-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8a0d1-109">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="8a0d1-110">**電子メール**アドレス`maria.rodriguez@contoso.com`と任意のパスワードを使用して、ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="8a0d1-111">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs*ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="8a0d1-112">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="8a0d1-113">構成</span><span class="sxs-lookup"><span data-stu-id="8a0d1-113">Configuration</span></span>

<span data-ttu-id="8a0d1-114">アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="8a0d1-115">`Startup.ConfigureServices`メソッドで、メソッド<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>と<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>メソッドを使用して認証ミドルウェアサービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="8a0d1-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>に`AddAuthentication`渡されたは、アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="8a0d1-117">`AuthenticationScheme`は、cookie 認証のインスタンスが複数存在し、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="8a0d1-118">`AuthenticationScheme`を[cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="8a0d1-119">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="8a0d1-120">アプリの認証方式は、アプリの cookie 認証スキームとは異なります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="8a0d1-121">クッキー認証スキームがに<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>指定されていない`CookieAuthenticationDefaults.AuthenticationScheme`場合は、(cookie) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="8a0d1-122">認証 cookie の<xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>プロパティは、既定で`true`に設定されています。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="8a0d1-123">サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="8a0d1-124">詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="8a0d1-125">で`Startup.Configure`、と`UseAuthentication` `UseAuthorization`を呼び出して、 `HttpContext.User`プロパティを設定し、要求の承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="8a0d1-126">を呼び出す`UseEndpoints`前`UseAuthorization`に、メソッドとメソッドを呼び出します。 `UseAuthentication`</span><span class="sxs-lookup"><span data-stu-id="8a0d1-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="8a0d1-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>クラスは、認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="8a0d1-128">メソッドの認証用にサービス構成でを設定`CookieAuthenticationOptions`します。 `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8a0d1-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="8a0d1-129">クッキーポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="8a0d1-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="8a0d1-130">Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="8a0d1-131">アプリ処理パイプラインへのミドルウェアの追加は順序に&mdash;依存しており、パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="8a0d1-132">Cookie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするために、クッキーポリシーミドルウェアに提供されているを使用します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="8a0d1-133">既定<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy>値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="8a0d1-134">と同じサイトポリシーを厳密に適用する`SameSiteMode.Strict`には`MinimumSameSitePolicy`、を設定します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="8a0d1-135">この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="8a0d1-136">の`MinimumSameSitePolicy` Cookie ポリシーミドルウェア設定は、次の表に`Cookie.SameSite`従っ`CookieAuthenticationOptions`て設定内のの設定に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="8a0d1-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="8a0d1-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="8a0d1-138">SameSite</span><span class="sxs-lookup"><span data-stu-id="8a0d1-138">Cookie.SameSite</span></span> | <span data-ttu-id="8a0d1-139">結果の SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="8a0d1-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="8a0d1-140">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-140">SameSiteMode.None</span></span>     | <span data-ttu-id="8a0d1-141">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-141">SameSiteMode.None</span></span><br><span data-ttu-id="8a0d1-142">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-143">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="8a0d1-144">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-144">SameSiteMode.None</span></span><br><span data-ttu-id="8a0d1-145">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-146">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="8a0d1-147">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="8a0d1-148">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-148">SameSiteMode.None</span></span><br><span data-ttu-id="8a0d1-149">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-150">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="8a0d1-151">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-152">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-153">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="8a0d1-154">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="8a0d1-155">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-155">SameSiteMode.None</span></span><br><span data-ttu-id="8a0d1-156">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-157">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="8a0d1-158">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="8a0d1-159">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="8a0d1-160">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="8a0d1-161">認証 cookie を作成する</span><span class="sxs-lookup"><span data-stu-id="8a0d1-161">Create an authentication cookie</span></span>

<span data-ttu-id="8a0d1-162">ユーザー情報を保持する cookie を作成するに<xref:System.Security.Claims.ClaimsPrincipal>は、を構築します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="8a0d1-163">ユーザー情報がシリアル化され、cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="8a0d1-164">任意の<xref:System.Security.Claims.ClaimsIdentity>が必要<xref:System.Security.Claims.Claim>なを使用し<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>てを作成し、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="8a0d1-165">`SignInAsync`暗号化されたクッキーを作成し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="8a0d1-166">が`AuthenticationScheme`指定されていない場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="8a0d1-167">暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="8a0d1-168">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="8a0d1-169">サインアウトする</span><span class="sxs-lookup"><span data-stu-id="8a0d1-169">Sign out</span></span>

<span data-ttu-id="8a0d1-170">現在のユーザーをサインアウトして cookie を削除するに<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>は、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="8a0d1-171">( `CookieAuthenticationDefaults.AuthenticationScheme`または "cookie") がスキームとして使用されていない場合 (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="8a0d1-172">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="8a0d1-173">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="8a0d1-173">React to back-end changes</span></span>

<span data-ttu-id="8a0d1-174">Cookie が作成されると、cookie は id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="8a0d1-175">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="8a0d1-176">アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="8a0d1-177">認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="8a0d1-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="8a0d1-179">すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="8a0d1-180">Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="8a0d1-181">ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="8a0d1-182">サンプルアプリでは、データベースはに`IUserRepository`実装され、値`LastChanged`を格納します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="8a0d1-183">データベースでユーザーが更新されると、 `LastChanged`値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="8a0d1-184">データベースが`LastChanged`値に基づいて変更されたときに cookie を無効にするには、 `LastChanged`データベースから現在`LastChanged`の値を含むクレームを使用して cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="8a0d1-185">`ValidatePrincipal`イベントのオーバーライドを実装するには、から<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>派生するクラスに次のシグネチャを持つメソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="8a0d1-186">の`CookieAuthenticationEvents`実装例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="8a0d1-187">Cookie サービスの登録時に、 `Startup.ConfigureServices`メソッドでイベントインスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8a0d1-188">クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定します。 `CustomCookieAuthenticationEvents`</span><span class="sxs-lookup"><span data-stu-id="8a0d1-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="8a0d1-189">ユーザーの名前が、セキュリティに影響しない決定&mdash;をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="8a0d1-190">ユーザープリンシパルを非破壊更新する場合は、を呼び出し`context.ReplacePrincipal` 、 `context.ShouldRenew`プロパティをに`true`設定します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="8a0d1-191">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="8a0d1-192">すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="8a0d1-193">永続的な cookie</span><span class="sxs-lookup"><span data-stu-id="8a0d1-193">Persistent cookies</span></span>

<span data-ttu-id="8a0d1-194">Cookie がブラウザーセッション間で保持されるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="8a0d1-195">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="8a0d1-196">次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="8a0d1-197">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="8a0d1-198">ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="8a0d1-199">を<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>に`true`設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="8a0d1-200">クッキーの絶対有効期限</span><span class="sxs-lookup"><span data-stu-id="8a0d1-200">Absolute cookie expiration</span></span>

<span data-ttu-id="8a0d1-201">絶対有効期限は、で<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>設定できます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="8a0d1-202">永続的なクッキーを作成する`IsPersistent`には、も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="8a0d1-203">それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="8a0d1-204">を`ExpiresUtc`設定すると、の<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>オプションの値がオーバーライドされます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="8a0d1-205">次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="8a0d1-206">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-206">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="8a0d1-207">ASP.NET Core Id は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="8a0d1-208">ただし、ASP.NET Core Id のない cookie ベースの認証認証プロバイダーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="8a0d1-209">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="8a0d1-210">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8a0d1-211">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="8a0d1-212">**電子メール**アドレス`maria.rodriguez@contoso.com`と任意のパスワードを使用して、ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="8a0d1-213">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs*ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="8a0d1-214">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="8a0d1-215">構成</span><span class="sxs-lookup"><span data-stu-id="8a0d1-215">Configuration</span></span>

<span data-ttu-id="8a0d1-216">アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="8a0d1-217">`Startup.ConfigureServices`メソッドで、メソッド<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>と<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>メソッドを使用して認証ミドルウェアサービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="8a0d1-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>に`AddAuthentication`渡されたは、アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="8a0d1-219">`AuthenticationScheme`は、cookie 認証のインスタンスが複数存在し、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="8a0d1-220">`AuthenticationScheme`を[cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="8a0d1-221">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="8a0d1-222">アプリの認証方式は、アプリの cookie 認証スキームとは異なります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="8a0d1-223">クッキー認証スキームがに<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>指定されていない`CookieAuthenticationDefaults.AuthenticationScheme`場合は、(cookie) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="8a0d1-224">認証 cookie の<xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>プロパティは、既定で`true`に設定されています。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="8a0d1-225">サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="8a0d1-226">詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="8a0d1-227">`Startup.Configure`メソッドで、 `UseAuthentication`メソッドを呼び出して、 `HttpContext.User`プロパティを設定する認証ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="8a0d1-228">または`UseAuthentication` `UseMvcWithDefaultRoute` `UseMvc`を呼び出す前に、メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="8a0d1-229"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>クラスは、認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="8a0d1-230">メソッドの認証用にサービス構成でを設定`CookieAuthenticationOptions`します。 `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8a0d1-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="8a0d1-231">クッキーポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="8a0d1-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="8a0d1-232">Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="8a0d1-233">アプリ処理パイプラインへのミドルウェアの追加は順序に&mdash;依存しており、パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="8a0d1-234">Cookie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするために、クッキーポリシーミドルウェアに提供されているを使用します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="8a0d1-235">既定<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy>値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="8a0d1-236">と同じサイトポリシーを厳密に適用する`SameSiteMode.Strict`には`MinimumSameSitePolicy`、を設定します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="8a0d1-237">この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="8a0d1-238">の`MinimumSameSitePolicy` Cookie ポリシーミドルウェア設定は、次の表に`Cookie.SameSite`従っ`CookieAuthenticationOptions`て設定内のの設定に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="8a0d1-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="8a0d1-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="8a0d1-240">SameSite</span><span class="sxs-lookup"><span data-stu-id="8a0d1-240">Cookie.SameSite</span></span> | <span data-ttu-id="8a0d1-241">結果の SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="8a0d1-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="8a0d1-242">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-242">SameSiteMode.None</span></span>     | <span data-ttu-id="8a0d1-243">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-243">SameSiteMode.None</span></span><br><span data-ttu-id="8a0d1-244">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-245">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="8a0d1-246">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-246">SameSiteMode.None</span></span><br><span data-ttu-id="8a0d1-247">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-248">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="8a0d1-249">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="8a0d1-250">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-250">SameSiteMode.None</span></span><br><span data-ttu-id="8a0d1-251">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-252">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="8a0d1-253">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-254">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-255">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="8a0d1-256">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="8a0d1-257">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="8a0d1-257">SameSiteMode.None</span></span><br><span data-ttu-id="8a0d1-258">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="8a0d1-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="8a0d1-259">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="8a0d1-260">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="8a0d1-261">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="8a0d1-262">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="8a0d1-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="8a0d1-263">認証 cookie を作成する</span><span class="sxs-lookup"><span data-stu-id="8a0d1-263">Create an authentication cookie</span></span>

<span data-ttu-id="8a0d1-264">ユーザー情報を保持する cookie を作成するに<xref:System.Security.Claims.ClaimsPrincipal>は、を構築します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="8a0d1-265">ユーザー情報がシリアル化され、cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="8a0d1-266">任意の<xref:System.Security.Claims.ClaimsIdentity>が必要<xref:System.Security.Claims.Claim>なを使用し<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>てを作成し、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="8a0d1-267">`SignInAsync`暗号化されたクッキーを作成し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="8a0d1-268">が`AuthenticationScheme`指定されていない場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="8a0d1-269">暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="8a0d1-270">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="8a0d1-271">サインアウトする</span><span class="sxs-lookup"><span data-stu-id="8a0d1-271">Sign out</span></span>

<span data-ttu-id="8a0d1-272">現在のユーザーをサインアウトして cookie を削除するに<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>は、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="8a0d1-273">( `CookieAuthenticationDefaults.AuthenticationScheme`または "cookie") がスキームとして使用されていない場合 (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="8a0d1-274">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="8a0d1-275">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="8a0d1-275">React to back-end changes</span></span>

<span data-ttu-id="8a0d1-276">Cookie が作成されると、cookie は id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="8a0d1-277">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="8a0d1-278">アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="8a0d1-279">認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="8a0d1-280"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="8a0d1-281">すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="8a0d1-282">Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="8a0d1-283">ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="8a0d1-284">サンプルアプリでは、データベースはに`IUserRepository`実装され、値`LastChanged`を格納します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="8a0d1-285">データベースでユーザーが更新されると、 `LastChanged`値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="8a0d1-286">データベースが`LastChanged`値に基づいて変更されたときに cookie を無効にするには、 `LastChanged`データベースから現在`LastChanged`の値を含むクレームを使用して cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="8a0d1-287">`ValidatePrincipal`イベントのオーバーライドを実装するには、から<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>派生するクラスに次のシグネチャを持つメソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="8a0d1-288">の`CookieAuthenticationEvents`実装例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="8a0d1-289">Cookie サービスの登録時に、 `Startup.ConfigureServices`メソッドでイベントインスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8a0d1-290">クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定します。 `CustomCookieAuthenticationEvents`</span><span class="sxs-lookup"><span data-stu-id="8a0d1-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="8a0d1-291">ユーザーの名前が、セキュリティに影響しない決定&mdash;をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="8a0d1-292">ユーザープリンシパルを非破壊更新する場合は、を呼び出し`context.ReplacePrincipal` 、 `context.ShouldRenew`プロパティをに`true`設定します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="8a0d1-293">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="8a0d1-294">すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="8a0d1-295">永続的な cookie</span><span class="sxs-lookup"><span data-stu-id="8a0d1-295">Persistent cookies</span></span>

<span data-ttu-id="8a0d1-296">Cookie がブラウザーセッション間で保持されるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="8a0d1-297">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="8a0d1-298">次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="8a0d1-299">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="8a0d1-300">ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="8a0d1-301">を<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>に`true`設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="8a0d1-302">クッキーの絶対有効期限</span><span class="sxs-lookup"><span data-stu-id="8a0d1-302">Absolute cookie expiration</span></span>

<span data-ttu-id="8a0d1-303">絶対有効期限は、で<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>設定できます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="8a0d1-304">永続的なクッキーを作成する`IsPersistent`には、も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="8a0d1-305">それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="8a0d1-306">を`ExpiresUtc`設定すると、の<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>オプションの値がオーバーライドされます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="8a0d1-307">次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="8a0d1-308">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="8a0d1-308">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="8a0d1-309">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8a0d1-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="8a0d1-310">ポリシーベースのロールチェック</span><span class="sxs-lookup"><span data-stu-id="8a0d1-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
