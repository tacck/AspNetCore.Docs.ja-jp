---
title: Facebook、Google、および外部プロバイダー認証 (ASP.NET Core なし)Identity
author: rick-anderson
description: ASP.NET Core のない Facebook、Google、Twitter などのアカウントユーザー認証の使用について説明し Identity ます。
ms.author: riande
ms.date: 12/10/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 73055a262ac69c0fd6a7f59e77d23121e71ea3dd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021667"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-no-locidentity"></a><span data-ttu-id="629db-103">ASP.NET Core せずにソーシャルサインインプロバイダー認証を使用するIdentity</span><span class="sxs-lookup"><span data-stu-id="629db-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="629db-104">[Kirk Larkin](https://twitter.com/serpent5)と[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="629db-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="629db-105"><xref:security/authentication/social/index>ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="629db-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="629db-106">このトピックで説明する方法には、 Identity 認証プロバイダーとしての ASP.NET Core が含まれています。</span><span class="sxs-lookup"><span data-stu-id="629db-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="629db-107">このサンプルでは、ASP.NET Core**なしで**外部認証プロバイダーを使用する方法を示し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="629db-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="629db-108">これは、ASP.NET Core のすべての機能を必要としない Identity が、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="629db-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="629db-109">このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="629db-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="629db-110">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="629db-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="629db-111">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="629db-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="629db-112">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="629db-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="629db-113">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="629db-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="629db-114">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="629db-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="629db-115">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="629db-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="629db-116">構成</span><span class="sxs-lookup"><span data-stu-id="629db-116">Configuration</span></span>

<span data-ttu-id="629db-117">メソッドで、 `ConfigureServices` 、、の各メソッドを使用して、アプリの認証方式を構成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> ます。</span><span class="sxs-lookup"><span data-stu-id="629db-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="629db-118">を呼び出すと、 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> アプリのが設定され <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> ます。</span><span class="sxs-lookup"><span data-stu-id="629db-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="629db-119">`DefaultScheme`は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="629db-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="629db-120">アプリの `DefaultScheme` を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") は、 Cookie これらの拡張メソッドの既定のスキームとして s を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="629db-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="629db-121">アプリのを <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="629db-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="629db-122">`DefaultChallengeScheme``DefaultScheme`をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="629db-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="629db-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>設定時にオーバーライドされる追加のプロパティについては、「」を参照してください `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="629db-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="629db-124">で、とを呼び出してから `Startup.Configure` `UseAuthentication` を呼び出し `UseAuthorization` `UseRouting` `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="629db-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="629db-125">これにより、プロパティが設定され、 `HttpContext.User` 要求の承認ミドルウェアが実行されます。</span><span class="sxs-lookup"><span data-stu-id="629db-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="629db-126">認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="629db-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="629db-127">認証の詳細につい cookie ては、「」を参照してください <xref:security/authentication/cookie> 。</span><span class="sxs-lookup"><span data-stu-id="629db-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="629db-128">承認の適用</span><span class="sxs-lookup"><span data-stu-id="629db-128">Apply authorization</span></span>

<span data-ttu-id="629db-129">`AuthorizeAttribute`コントローラー、アクション、またはページに属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="629db-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="629db-130">次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="629db-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="629db-131">サインアウト</span><span class="sxs-lookup"><span data-stu-id="629db-131">Sign out</span></span>

<span data-ttu-id="629db-132">現在のユーザーをサインアウトし、を削除するに cookie は、 [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="629db-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="629db-133">次のコードは、 `Logout` *インデックス*ページにページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="629db-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="629db-134">への呼び出しで認証スキームが指定されていないことに注意して `SignOutAsync` ください。</span><span class="sxs-lookup"><span data-stu-id="629db-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="629db-135">`DefaultScheme`のアプリ `CookieAuthenticationDefaults.AuthenticationScheme` はフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="629db-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="629db-136">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="629db-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="629db-137"><xref:security/authentication/social/index>ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="629db-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="629db-138">このトピックで説明する方法には、 Identity 認証プロバイダーとしての ASP.NET Core が含まれています。</span><span class="sxs-lookup"><span data-stu-id="629db-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="629db-139">このサンプルでは、ASP.NET Core**なしで**外部認証プロバイダーを使用する方法を示し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="629db-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="629db-140">これは、ASP.NET Core のすべての機能を必要としない Identity が、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="629db-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="629db-141">このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="629db-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="629db-142">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="629db-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="629db-143">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="629db-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="629db-144">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="629db-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="629db-145">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="629db-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="629db-146">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="629db-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="629db-147">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="629db-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="629db-148">構成</span><span class="sxs-lookup"><span data-stu-id="629db-148">Configuration</span></span>

<span data-ttu-id="629db-149">メソッドで、 `ConfigureServices` 、、の各メソッドを使用して、アプリの認証方式を構成し `AddAuthentication` `AddCookie` `AddGoogle` ます。</span><span class="sxs-lookup"><span data-stu-id="629db-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="629db-150">[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)を呼び出すと、アプリの[defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)が設定されます。</span><span class="sxs-lookup"><span data-stu-id="629db-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="629db-151">`DefaultScheme`は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="629db-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="629db-152">アプリの `DefaultScheme` を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") は、 Cookie これらの拡張メソッドの既定のスキームとして s を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="629db-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="629db-153">アプリのを <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="629db-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="629db-154">`DefaultChallengeScheme``DefaultScheme`をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="629db-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="629db-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>設定時にオーバーライドされる追加のプロパティについては、「」を参照してください `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="629db-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="629db-156">メソッドで、 `Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。</span><span class="sxs-lookup"><span data-stu-id="629db-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="629db-157">`UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="629db-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="629db-158">認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="629db-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="629db-159">認証の詳細につい cookie ては、「」を参照してください <xref:security/authentication/cookie> 。</span><span class="sxs-lookup"><span data-stu-id="629db-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="629db-160">承認の適用</span><span class="sxs-lookup"><span data-stu-id="629db-160">Apply authorization</span></span>

<span data-ttu-id="629db-161">`AuthorizeAttribute`コントローラー、アクション、またはページに属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="629db-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="629db-162">次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="629db-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="629db-163">サインアウト</span><span class="sxs-lookup"><span data-stu-id="629db-163">Sign out</span></span>

<span data-ttu-id="629db-164">現在のユーザーをサインアウトし、を削除するに cookie は、 [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="629db-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="629db-165">次のコードは、 `Logout` *インデックス*ページにページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="629db-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="629db-166">への呼び出しで認証スキームが指定されていないことに注意して `SignOutAsync` ください。</span><span class="sxs-lookup"><span data-stu-id="629db-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="629db-167">`DefaultScheme`のアプリ `CookieAuthenticationDefaults.AuthenticationScheme` はフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="629db-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="629db-168">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="629db-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
