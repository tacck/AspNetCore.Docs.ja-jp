---
title: '認証を使用 :::no-loc(cookie)::: しない :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: 'を使用せずに認証を使用する方法について説明 :::no-loc(cookie)::: :::no-loc(ASP.NET Core Identity)::: します。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: 'security/authentication/:::no-loc(cookie):::'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061354"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="155e3-103">認証を使用 :::no-loc(cookie)::: しない :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="155e3-103">Use :::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="155e3-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="155e3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="155e3-105">:::no-loc(ASP.NET Core Identity)::: は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="155e3-105">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="155e3-106">ただし、を :::no-loc(cookie)::: 使用しないベースの認証プロバイダーを :::no-loc(ASP.NET Core Identity)::: 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="155e3-106">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="155e3-107">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="155e3-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="155e3-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="155e3-109">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="155e3-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="155e3-110">**電子メール** アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="155e3-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="155e3-111">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="155e3-112">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="155e3-113">構成</span><span class="sxs-lookup"><span data-stu-id="155e3-113">Configuration</span></span>

<span data-ttu-id="155e3-114">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="155e3-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="155e3-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="155e3-116">`AuthenticationScheme` は、認証の複数のインスタンスがあり :::no-loc(cookie)::: 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="155e3-116">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="155e3-117">`AuthenticationScheme`を[ :::no-loc(Cookie)::: authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 :::no-loc(Cookie):::</span><span class="sxs-lookup"><span data-stu-id="155e3-117">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="155e3-118">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="155e3-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="155e3-119">アプリの認証方式は、アプリの認証スキームとは異なり :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-119">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="155e3-120">:::no-loc(cookie):::に認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> 、 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-120">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="155e3-121">認証 :::no-loc(cookie)::: の <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="155e3-121">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="155e3-122">:::no-loc(cookie):::サイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-122">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="155e3-123">詳細については、「<xref:security/gdpr#essential-:::no-loc(cookie):::s>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-123">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="155e3-124">で、とを呼び出して、 `Startup.Configure` `UseAuthentication` プロパティを `UseAuthorization` 設定し、 `HttpContext.User` 要求の承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="155e3-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="155e3-125">`UseAuthentication`を `UseAuthorization` 呼び出す前に、メソッドとメソッドを呼び出し `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="155e3-126">クラスは、 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-126">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="155e3-127">`:::no-loc(Cookie):::AuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-127">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="155e3-128">:::no-loc(Cookie)::: ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="155e3-128">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="155e3-129">ポリシー [ :::no-loc(Cookie)::: ミドルウェア](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware)は :::no-loc(cookie)::: 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="155e3-129">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="155e3-130">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="155e3-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="155e3-131"><xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions>ポリシーミドルウェアに提供されるを使用して、 :::no-loc(Cookie)::: :::no-loc(cookie)::: :::no-loc(cookie)::: :::no-loc(cookie)::: が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="155e3-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="155e3-132">既定 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="155e3-132">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="155e3-133">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="155e3-134">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 :::no-loc(cookie)::: クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="155e3-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="155e3-135">:::no-loc(Cookie):::のポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `:::no-loc(Cookie):::.SameSite` `:::no-loc(Cookie):::AuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="155e3-135">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="155e3-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="155e3-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="155e3-137">:::no-loc(Cookie):::.SameSite</span><span class="sxs-lookup"><span data-stu-id="155e3-137">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="155e3-138">結果 :::no-loc(Cookie)::: 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="155e3-138">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="155e3-139">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-139">SameSiteMode.None</span></span>     | <span data-ttu-id="155e3-140">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-140">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-141">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-142">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-143">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-143">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-144">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-145">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="155e3-146">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="155e3-147">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-147">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-148">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-149">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-150">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-151">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-152">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="155e3-153">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="155e3-154">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-154">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-155">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-156">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-157">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="155e3-158">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="155e3-159">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="155e3-160">認証を作成する :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="155e3-160">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="155e3-161">保持しているユーザー情報を作成するに :::no-loc(cookie)::: は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="155e3-161">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="155e3-162">ユーザー情報はにシリアル化され、に格納され :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-162">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="155e3-163">任意の <xref:System.Security.Claims.Claims:::no-loc(Identity):::> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="155e3-163">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="155e3-164">`SignInAsync` 暗号化されたを作成 :::no-loc(cookie)::: し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="155e3-164">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="155e3-165">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="155e3-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> は、既定ではいくつかの特定のパス (ログインパスやログアウトパスなど) でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="155e3-167">詳細については、「 [ :::no-loc(Cookie)::: authenticationhandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-167">For more information see the [:::no-loc(Cookie):::AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="155e3-168">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="155e3-169">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="155e3-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="155e3-170">サインアウト</span><span class="sxs-lookup"><span data-stu-id="155e3-170">Sign out</span></span>

<span data-ttu-id="155e3-171">現在のユーザーをサインアウトして削除するには :::no-loc(cookie)::: 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-171">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="155e3-172">`:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`(または " :::no-loc(Cookie)::: s") がスキームとして使用されていない場合 ("Contoso :::no-loc(Cookie)::: " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="155e3-172">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="155e3-173">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="155e3-174">ブラウザーが閉じると、セッションベースのが自動的に削除 :::no-loc(cookie)::: され :::no-loc(cookie)::: ますが (非持続 s)、個々のタブが閉じられても、 :::no-loc(cookie)::: はクリアされません。</span><span class="sxs-lookup"><span data-stu-id="155e3-174">When the browser closes it automatically deletes session based :::no-loc(cookie):::s (non-persistent :::no-loc(cookie):::s), but no :::no-loc(cookie):::s are cleared when an individual tab is closed.</span></span> <span data-ttu-id="155e3-175">サーバーには、タブまたはブラウザーの終了イベントは通知されません。</span><span class="sxs-lookup"><span data-stu-id="155e3-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="155e3-176">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="155e3-176">React to back-end changes</span></span>

<span data-ttu-id="155e3-177">が作成されると、は :::no-loc(cookie)::: :::no-loc(cookie)::: id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="155e3-177">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="155e3-178">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="155e3-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="155e3-179">アプリの :::no-loc(cookie)::: 認証システムは、認証に基づいて要求を処理し続け :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-179">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="155e3-180">認証が有効である限り、ユーザーはアプリにサインインしたままに :::no-loc(cookie)::: なります。</span><span class="sxs-lookup"><span data-stu-id="155e3-180">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="155e3-181">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-181">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="155e3-182">すべての要求でを検証すると、 :::no-loc(cookie)::: アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-182">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="155e3-183">検証の方法 :::no-loc(cookie)::: の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="155e3-183">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="155e3-184">ユーザーの発行後にデータベースが変更されていない場合は、 :::no-loc(cookie)::: ユーザー :::no-loc(cookie)::: がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="155e3-184">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="155e3-185">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="155e3-186">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="155e3-187">値に基づいてデータベースが変更されたときにを無効にするには、 :::no-loc(cookie)::: データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し :::no-loc(cookie)::: `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-187">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="155e3-188">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="155e3-189">の実装例を次に示し `:::no-loc(Cookie):::AuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-189">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="155e3-190">サービス登録時にイベントインスタンスを :::no-loc(cookie)::: メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="155e3-190">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="155e3-191">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `Custom:::no-loc(Cookie):::AuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="155e3-192">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="155e3-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="155e3-193">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="155e3-194">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="155e3-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="155e3-195">:::no-loc(cookie):::すべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-195">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="155e3-196">永続的な :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="155e3-196">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="155e3-197">:::no-loc(cookie):::をブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="155e3-197">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="155e3-198">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="155e3-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="155e3-199">次のコードスニペットは、 :::no-loc(cookie)::: ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="155e3-199">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="155e3-200">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="155e3-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="155e3-201">ブラウザーを閉じている間にが :::no-loc(cookie)::: 期限切れになると、ブラウザーは再起動後にをクリアし :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-201">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="155e3-202"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="155e3-203">絶対 :::no-loc(cookie)::: 有効期限</span><span class="sxs-lookup"><span data-stu-id="155e3-203">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="155e3-204">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="155e3-205">永続的なを作成するには :::no-loc(cookie)::: 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-205">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="155e3-206">それ以外の場合、は :::no-loc(cookie)::: セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-206">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="155e3-207">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="155e3-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="155e3-208">次のコードスニペットでは、 :::no-loc(cookie)::: 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="155e3-208">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="155e3-209">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="155e3-210">:::no-loc(ASP.NET Core Identity)::: は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="155e3-210">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="155e3-211">ただし、を :::no-loc(cookie)::: 使用しないベースの認証プロバイダーを :::no-loc(ASP.NET Core Identity)::: 使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="155e3-211">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="155e3-212">詳細については、「<xref:security/authentication/identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="155e3-213">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="155e3-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="155e3-214">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="155e3-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="155e3-215">**電子メール** アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="155e3-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="155e3-216">ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs* ファイルのメソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="155e3-217">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="155e3-218">構成</span><span class="sxs-lookup"><span data-stu-id="155e3-218">Configuration</span></span>

<span data-ttu-id="155e3-219">アプリで [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore :::no-loc(Cookie)::: のプロジェクトファイルにパッケージ参照を作成します。s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="155e3-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) package.</span></span>

<span data-ttu-id="155e3-220">メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="155e3-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="155e3-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="155e3-222">`AuthenticationScheme` は、認証の複数のインスタンスがあり :::no-loc(cookie)::: 、 [特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="155e3-222">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="155e3-223">`AuthenticationScheme`を[ :::no-loc(Cookie)::: authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)は、スキームの値として "s" を提供します。 :::no-loc(Cookie):::</span><span class="sxs-lookup"><span data-stu-id="155e3-223">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="155e3-224">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="155e3-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="155e3-225">アプリの認証方式は、アプリの認証スキームとは異なり :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-225">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="155e3-226">:::no-loc(cookie):::に認証スキームが指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> 、 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-226">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="155e3-227">認証 :::no-loc(cookie)::: の <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> プロパティは、既定でに設定され `true` ています。</span><span class="sxs-lookup"><span data-stu-id="155e3-227">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="155e3-228">:::no-loc(cookie):::サイトビジターがデータコレクションに同意していない場合は、認証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-228">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="155e3-229">詳細については、「<xref:security/gdpr#essential-:::no-loc(cookie):::s>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="155e3-229">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="155e3-230">メソッドで、 `Startup.Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="155e3-231">`UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="155e3-232">クラスは、 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-232">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="155e3-233">`:::no-loc(Cookie):::AuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-233">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="155e3-234">:::no-loc(Cookie)::: ポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="155e3-234">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="155e3-235">ポリシー [ :::no-loc(Cookie)::: ミドルウェア](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware)は :::no-loc(cookie)::: 、ポリシー機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="155e3-235">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="155e3-236">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="155e3-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="155e3-237"><xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions>ポリシーミドルウェアに提供されるを使用して、 :::no-loc(Cookie)::: :::no-loc(cookie)::: :::no-loc(cookie)::: :::no-loc(cookie)::: が追加または削除されたときの処理ハンドラーへの処理とフックのグローバル特性を制御します。</span><span class="sxs-lookup"><span data-stu-id="155e3-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="155e3-238">既定 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。</span><span class="sxs-lookup"><span data-stu-id="155e3-238">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="155e3-239">と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="155e3-240">この設定は、OAuth2 やその他のクロスオリジン認証スキームを分割しますが、 :::no-loc(cookie)::: クロスオリジン要求処理に依存しない他の種類のアプリのセキュリティレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="155e3-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="155e3-241">:::no-loc(Cookie):::のポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` に従って設定のの設定に影響する場合が `:::no-loc(Cookie):::.SameSite` `:::no-loc(Cookie):::AuthenticationOptions` あります。</span><span class="sxs-lookup"><span data-stu-id="155e3-241">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="155e3-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="155e3-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="155e3-243">:::no-loc(Cookie):::.SameSite</span><span class="sxs-lookup"><span data-stu-id="155e3-243">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="155e3-244">結果 :::no-loc(Cookie)::: 。SameSite の設定</span><span class="sxs-lookup"><span data-stu-id="155e3-244">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="155e3-245">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-245">SameSiteMode.None</span></span>     | <span data-ttu-id="155e3-246">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-246">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-247">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-248">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-249">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-249">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-250">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-251">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="155e3-252">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="155e3-253">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-253">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-254">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-255">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-256">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-257">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-258">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="155e3-259">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="155e3-260">SameSiteMode なし</span><span class="sxs-lookup"><span data-stu-id="155e3-260">SameSiteMode.None</span></span><br><span data-ttu-id="155e3-261">SameSiteMode 厳密でない</span><span class="sxs-lookup"><span data-stu-id="155e3-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="155e3-262">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="155e3-263">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="155e3-264">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="155e3-265">SameSiteMode Strict</span><span class="sxs-lookup"><span data-stu-id="155e3-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="155e3-266">認証を作成する :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="155e3-266">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="155e3-267">保持しているユーザー情報を作成するに :::no-loc(cookie)::: は、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。</span><span class="sxs-lookup"><span data-stu-id="155e3-267">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="155e3-268">ユーザー情報はにシリアル化され、に格納され :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-268">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="155e3-269">任意の <xref:System.Security.Claims.Claims:::no-loc(Identity):::> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="155e3-269">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="155e3-270">`SignInAsync` 暗号化されたを作成 :::no-loc(cookie)::: し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="155e3-270">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="155e3-271">が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="155e3-272">暗号化には ASP.NET Core の [データ保護](xref:security/data-protection/using-data-protection) システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="155e3-273">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように [データ保護を構成](xref:security/data-protection/configuration/overview) します。</span><span class="sxs-lookup"><span data-stu-id="155e3-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="155e3-274">サインアウト</span><span class="sxs-lookup"><span data-stu-id="155e3-274">Sign out</span></span>

<span data-ttu-id="155e3-275">現在のユーザーをサインアウトして削除するには :::no-loc(cookie)::: 、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-275">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="155e3-276">`:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`(または " :::no-loc(Cookie)::: s") がスキームとして使用されていない場合 ("Contoso :::no-loc(Cookie)::: " など)、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="155e3-276">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="155e3-277">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="155e3-278">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="155e3-278">React to back-end changes</span></span>

<span data-ttu-id="155e3-279">が作成されると、は :::no-loc(cookie)::: :::no-loc(cookie)::: id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="155e3-279">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="155e3-280">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="155e3-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="155e3-281">アプリの :::no-loc(cookie)::: 認証システムは、認証に基づいて要求を処理し続け :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-281">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="155e3-282">認証が有効である限り、ユーザーはアプリにサインインしたままに :::no-loc(cookie)::: なります。</span><span class="sxs-lookup"><span data-stu-id="155e3-282">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="155e3-283">イベントを使用して <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> 、id の検証をインターセプトおよびオーバーライドでき :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-283">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="155e3-284">すべての要求でを検証すると、 :::no-loc(cookie)::: アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-284">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="155e3-285">検証の方法 :::no-loc(cookie)::: の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="155e3-285">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="155e3-286">ユーザーの発行後にデータベースが変更されていない場合は、 :::no-loc(cookie)::: ユーザー :::no-loc(cookie)::: がまだ有効である場合は、再認証を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="155e3-286">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="155e3-287">サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="155e3-288">データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="155e3-289">値に基づいてデータベースが変更されたときにを無効にするには、 :::no-loc(cookie)::: データベースの現在の値を `LastChanged` 含むクレームを使用してを作成し :::no-loc(cookie)::: `LastChanged` `LastChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-289">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="155e3-290">イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="155e3-291">の実装例を次に示し `:::no-loc(Cookie):::AuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-291">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="155e3-292">サービス登録時にイベントインスタンスを :::no-loc(cookie)::: メソッドに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="155e3-292">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="155e3-293">クラスの [スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes) を指定し `Custom:::no-loc(Cookie):::AuthenticationEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="155e3-294">ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="155e3-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="155e3-295">ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="155e3-296">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="155e3-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="155e3-297">:::no-loc(cookie):::すべての要求に対して認証を検証すると、アプリケーションのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-297">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="155e3-298">永続的な :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="155e3-298">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="155e3-299">:::no-loc(cookie):::をブラウザーセッション間で永続化することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="155e3-299">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="155e3-300">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="155e3-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="155e3-301">次のコードスニペットは、 :::no-loc(cookie)::: ブラウザーのクロージャを通過する id と対応するを作成します。</span><span class="sxs-lookup"><span data-stu-id="155e3-301">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="155e3-302">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="155e3-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="155e3-303">ブラウザーを閉じている間にが :::no-loc(cookie)::: 期限切れになると、ブラウザーは再起動後にをクリアし :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-303">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="155e3-304"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="155e3-305">絶対 :::no-loc(cookie)::: 有効期限</span><span class="sxs-lookup"><span data-stu-id="155e3-305">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="155e3-306">絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。</span><span class="sxs-lookup"><span data-stu-id="155e3-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="155e3-307">永続的なを作成するには :::no-loc(cookie)::: 、 `IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-307">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="155e3-308">それ以外の場合、は :::no-loc(cookie)::: セッションベースの有効期間で作成され、保持する認証チケットの前または後に期限切れになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="155e3-308">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="155e3-309">を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions> ます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="155e3-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="155e3-310">次のコードスニペットでは、 :::no-loc(cookie)::: 20 分間継続する id と対応する id を作成します。</span><span class="sxs-lookup"><span data-stu-id="155e3-310">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="155e3-311">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="155e3-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
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
