---
title: Facebook、Google、および外部プロバイダー認証 (ASP.NET Core なし)Identity
author: rick-anderson
description: ASP.NET Core Identityのない Facebook、Google、Twitter などのアカウントユーザー認証の使用について説明します。
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cc44eb83947540ca9a5a04ffad4fdb8522fab26a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775740"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="a4ba5-103">ASP.NET Core せずにソーシャルサインインプロバイダー認証を使用するIdentity</span><span class="sxs-lookup"><span data-stu-id="a4ba5-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="a4ba5-104">[Kirk Larkin](https://twitter.com/serpent5)と[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a4ba5-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a4ba5-105"><xref:security/authentication/social/index>ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="a4ba5-106">このトピックで説明する方法にはIdentity 、認証プロバイダーとしての ASP.NET Core が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="a4ba5-107">このサンプルでは、ASP.NET Core Identity**なしで**外部認証プロバイダーを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="a4ba5-108">これは、ASP.NET Core Identityのすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="a4ba5-109">このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="a4ba5-110">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="a4ba5-111">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="a4ba5-112">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="a4ba5-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="a4ba5-113">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="a4ba5-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="a4ba5-114">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="a4ba5-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="a4ba5-115">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="a4ba5-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="a4ba5-116">構成</span><span class="sxs-lookup"><span data-stu-id="a4ba5-116">Configuration</span></span>

<span data-ttu-id="a4ba5-117">`ConfigureServices`メソッドで、 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>、 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>、の各<xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>メソッドを使用して、アプリの認証方式を構成します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="a4ba5-118">を<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>呼び出すと、アプリの<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>が設定されます。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="a4ba5-119">`DefaultScheme`は、次`HttpContext`の認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="a4ba5-120">アプリの`DefaultScheme`を[cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookie") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="a4ba5-121">アプリの<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に`ChallengeAsync`対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="a4ba5-122">`DefaultChallengeScheme`を`DefaultScheme`オーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="a4ba5-123">設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>時にオーバーライド`DefaultScheme`される追加のプロパティについては、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="a4ba5-124">`Startup.Configure` `UseRouting`で、とを呼び出してから`UseEndpoints`を呼び出します。 `UseAuthentication` `UseAuthorization`</span><span class="sxs-lookup"><span data-stu-id="a4ba5-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="a4ba5-125">これにより`HttpContext.User` 、プロパティが設定され、要求の承認ミドルウェアが実行されます。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="a4ba5-126">認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="a4ba5-127">Cookie 認証の詳細については<xref:security/authentication/cookie>、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="a4ba5-128">承認の適用</span><span class="sxs-lookup"><span data-stu-id="a4ba5-128">Apply authorization</span></span>

<span data-ttu-id="a4ba5-129">コントローラー、アクション、またはページに`AuthorizeAttribute`属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="a4ba5-130">次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="a4ba5-131">サインアウトする</span><span class="sxs-lookup"><span data-stu-id="a4ba5-131">Sign out</span></span>

<span data-ttu-id="a4ba5-132">現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="a4ba5-133">次のコードは、 `Logout` *インデックス*ページにページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="a4ba5-134">へ`SignOutAsync`の呼び出しで認証スキームが指定されていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="a4ba5-135">`DefaultScheme`の`CookieAuthenticationDefaults.AuthenticationScheme`アプリはフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4ba5-136">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a4ba5-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a4ba5-137"><xref:security/authentication/social/index>ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="a4ba5-138">このトピックで説明する方法にはIdentity 、認証プロバイダーとしての ASP.NET Core が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="a4ba5-139">このサンプルでは、ASP.NET Core Identity**なしで**外部認証プロバイダーを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="a4ba5-140">これは、ASP.NET Core Identityのすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="a4ba5-141">このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="a4ba5-142">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="a4ba5-143">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="a4ba5-144">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="a4ba5-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="a4ba5-145">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="a4ba5-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="a4ba5-146">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="a4ba5-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="a4ba5-147">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="a4ba5-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="a4ba5-148">構成</span><span class="sxs-lookup"><span data-stu-id="a4ba5-148">Configuration</span></span>

<span data-ttu-id="a4ba5-149">`ConfigureServices`メソッドで、 `AddAuthentication`、 `AddCookie`、の各`AddGoogle`メソッドを使用して、アプリの認証方式を構成します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="a4ba5-150">[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)を呼び出すと、アプリの[defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)が設定されます。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="a4ba5-151">`DefaultScheme`は、次`HttpContext`の認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="a4ba5-152">アプリの`DefaultScheme`を[cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookie") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="a4ba5-153">アプリの<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に`ChallengeAsync`対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="a4ba5-154">`DefaultChallengeScheme`を`DefaultScheme`オーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="a4ba5-155">設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>時にオーバーライド`DefaultScheme`される追加のプロパティについては、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="a4ba5-156">`Configure`メソッドで、 `UseAuthentication`メソッドを呼び出して、 `HttpContext.User`プロパティを設定する認証ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="a4ba5-157">または`UseAuthentication` `UseMvcWithDefaultRoute` `UseMvc`を呼び出す前に、メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="a4ba5-158">認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="a4ba5-159">Cookie 認証の詳細については<xref:security/authentication/cookie>、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="a4ba5-160">承認の適用</span><span class="sxs-lookup"><span data-stu-id="a4ba5-160">Apply authorization</span></span>

<span data-ttu-id="a4ba5-161">コントローラー、アクション、またはページに`AuthorizeAttribute`属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="a4ba5-162">次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="a4ba5-163">サインアウトする</span><span class="sxs-lookup"><span data-stu-id="a4ba5-163">Sign out</span></span>

<span data-ttu-id="a4ba5-164">現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="a4ba5-165">次のコードは、 `Logout` *インデックス*ページにページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="a4ba5-166">へ`SignOutAsync`の呼び出しで認証スキームが指定されていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="a4ba5-167">`DefaultScheme`の`CookieAuthenticationDefaults.AuthenticationScheme`アプリはフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="a4ba5-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4ba5-168">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a4ba5-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
