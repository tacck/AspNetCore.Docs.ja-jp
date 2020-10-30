---
title: 'Facebook、Google、および外部プロバイダー認証 (なし) :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: 'Facebook、Google、Twitter などのアカウントユーザー認証を使用しない場合の説明 :::no-loc(ASP.NET Core Identity)::: 。'
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060288"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="facf6-103">ソーシャルサインインプロバイダー認証を使用しない :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="facf6-103">Use social sign-in provider authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="facf6-104">[Kirk Larkin](https://twitter.com/serpent5)と[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="facf6-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="facf6-105"><xref:security/authentication/social/index> ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="facf6-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="facf6-106">このトピックで説明する方法は、 :::no-loc(ASP.NET Core Identity)::: 認証プロバイダーとして含まれています。</span><span class="sxs-lookup"><span data-stu-id="facf6-106">The approach described in that topic includes :::no-loc(ASP.NET Core Identity)::: as an authentication provider.</span></span>

<span data-ttu-id="facf6-107">このサンプルでは、を使用 **せず** に外部認証プロバイダーを使用する方法を示し :::no-loc(ASP.NET Core Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="facf6-107">This sample demonstrates how to use an external authentication provider **without** :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="facf6-108">これは、のすべての機能を必要としない :::no-loc(ASP.NET Core Identity)::: が、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="facf6-108">This is useful for apps that don't require all of the features of :::no-loc(ASP.NET Core Identity):::, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="facf6-109">このサンプルでは、ユーザーの認証に [Google 認証](xref:security/authentication/google-logins) を使用します。</span><span class="sxs-lookup"><span data-stu-id="facf6-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="facf6-110">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="facf6-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="facf6-111">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="facf6-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="facf6-112">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="facf6-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="facf6-113">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="facf6-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="facf6-114">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="facf6-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="facf6-115">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="facf6-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="facf6-116">構成</span><span class="sxs-lookup"><span data-stu-id="facf6-116">Configuration</span></span>

<span data-ttu-id="facf6-117">メソッドで、 `ConfigureServices` 、、の各メソッドを使用して、アプリの認証方式を構成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> ます。</span><span class="sxs-lookup"><span data-stu-id="facf6-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="facf6-118">を呼び出すと、 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> アプリのが設定され <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> ます。</span><span class="sxs-lookup"><span data-stu-id="facf6-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="facf6-119">`DefaultScheme`は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="facf6-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="facf6-120">アプリの `DefaultScheme` を[ :::no-loc(Cookie)::: authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (" :::no-loc(Cookie)::: s") は、 :::no-loc(Cookie)::: これらの拡張メソッドの既定のスキームとして s を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="facf6-120">Setting the app's `DefaultScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (":::no-loc(Cookie):::s") configures the app to use :::no-loc(Cookie):::s as the default scheme for these extension methods.</span></span> <span data-ttu-id="facf6-121">アプリのを <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="facf6-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="facf6-122">`DefaultChallengeScheme``DefaultScheme`をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="facf6-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="facf6-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>設定時にオーバーライドされる追加のプロパティについては、「」を参照してください `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="facf6-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="facf6-124">で、とを呼び出してから `Startup.Configure` `UseAuthentication` を呼び出し `UseAuthorization` `UseRouting` `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="facf6-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="facf6-125">これにより、プロパティが設定され、 `HttpContext.User` 要求の承認ミドルウェアが実行されます。</span><span class="sxs-lookup"><span data-stu-id="facf6-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="facf6-126">認証方式の詳細については、「 [認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="facf6-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="facf6-127">認証の詳細につい :::no-loc(cookie)::: ては、「」を参照してください <xref:security/authentication/:::no-loc(cookie):::> 。</span><span class="sxs-lookup"><span data-stu-id="facf6-127">To learn more about :::no-loc(cookie)::: authentication, see <xref:security/authentication/:::no-loc(cookie):::>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="facf6-128">承認の適用</span><span class="sxs-lookup"><span data-stu-id="facf6-128">Apply authorization</span></span>

<span data-ttu-id="facf6-129">`AuthorizeAttribute`コントローラー、アクション、またはページに属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="facf6-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="facf6-130">次のコードは、認証されているユーザーへの *プライバシー* ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="facf6-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="facf6-131">サインアウト</span><span class="sxs-lookup"><span data-stu-id="facf6-131">Sign out</span></span>

<span data-ttu-id="facf6-132">現在のユーザーをサインアウトし、を削除するに :::no-loc(cookie)::: は、 [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="facf6-132">To sign out the current user and delete their :::no-loc(cookie):::, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="facf6-133">次のコードは、 `Logout` *インデックス* ページにページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="facf6-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="facf6-134">への呼び出しで認証スキームが指定されていないことに注意して `SignOutAsync` ください。</span><span class="sxs-lookup"><span data-stu-id="facf6-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="facf6-135">`DefaultScheme`のアプリ `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` はフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="facf6-135">The app's `DefaultScheme` of `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="facf6-136">その他の資料</span><span class="sxs-lookup"><span data-stu-id="facf6-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="facf6-137"><xref:security/authentication/social/index> ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="facf6-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="facf6-138">このトピックで説明する方法は、 :::no-loc(ASP.NET Core Identity)::: 認証プロバイダーとして含まれています。</span><span class="sxs-lookup"><span data-stu-id="facf6-138">The approach described in that topic includes :::no-loc(ASP.NET Core Identity)::: as an authentication provider.</span></span>

<span data-ttu-id="facf6-139">このサンプルでは、を使用 **せず** に外部認証プロバイダーを使用する方法を示し :::no-loc(ASP.NET Core Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="facf6-139">This sample demonstrates how to use an external authentication provider **without** :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="facf6-140">これは、のすべての機能を必要としない :::no-loc(ASP.NET Core Identity)::: が、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="facf6-140">This is useful for apps that don't require all of the features of :::no-loc(ASP.NET Core Identity):::, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="facf6-141">このサンプルでは、ユーザーの認証に [Google 認証](xref:security/authentication/google-logins) を使用します。</span><span class="sxs-lookup"><span data-stu-id="facf6-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="facf6-142">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="facf6-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="facf6-143">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="facf6-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="facf6-144">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="facf6-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="facf6-145">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="facf6-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="facf6-146">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="facf6-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="facf6-147">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="facf6-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="facf6-148">構成</span><span class="sxs-lookup"><span data-stu-id="facf6-148">Configuration</span></span>

<span data-ttu-id="facf6-149">メソッドで、 `ConfigureServices` 、、の各メソッドを使用して、アプリの認証方式を構成し `AddAuthentication` `Add:::no-loc(Cookie):::` `AddGoogle` ます。</span><span class="sxs-lookup"><span data-stu-id="facf6-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `Add:::no-loc(Cookie):::`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="facf6-150">[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)を呼び出すと、アプリの[defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)が設定されます。</span><span class="sxs-lookup"><span data-stu-id="facf6-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="facf6-151">`DefaultScheme`は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="facf6-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="facf6-152">アプリの `DefaultScheme` を[ :::no-loc(Cookie)::: authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (" :::no-loc(Cookie)::: s") は、 :::no-loc(Cookie)::: これらの拡張メソッドの既定のスキームとして s を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="facf6-152">Setting the app's `DefaultScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (":::no-loc(Cookie):::s") configures the app to use :::no-loc(Cookie):::s as the default scheme for these extension methods.</span></span> <span data-ttu-id="facf6-153">アプリのを <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="facf6-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="facf6-154">`DefaultChallengeScheme``DefaultScheme`をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="facf6-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="facf6-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>設定時にオーバーライドされる追加のプロパティについては、「」を参照してください `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="facf6-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="facf6-156">メソッドで、 `Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。</span><span class="sxs-lookup"><span data-stu-id="facf6-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="facf6-157">`UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="facf6-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="facf6-158">認証方式の詳細については、「 [認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="facf6-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="facf6-159">認証の詳細につい :::no-loc(cookie)::: ては、「」を参照してください <xref:security/authentication/:::no-loc(cookie):::> 。</span><span class="sxs-lookup"><span data-stu-id="facf6-159">To learn more about :::no-loc(cookie)::: authentication, see <xref:security/authentication/:::no-loc(cookie):::>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="facf6-160">承認の適用</span><span class="sxs-lookup"><span data-stu-id="facf6-160">Apply authorization</span></span>

<span data-ttu-id="facf6-161">`AuthorizeAttribute`コントローラー、アクション、またはページに属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="facf6-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="facf6-162">次のコードは、認証されているユーザーへの *プライバシー* ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="facf6-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="facf6-163">サインアウト</span><span class="sxs-lookup"><span data-stu-id="facf6-163">Sign out</span></span>

<span data-ttu-id="facf6-164">現在のユーザーをサインアウトし、を削除するに :::no-loc(cookie)::: は、 [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="facf6-164">To sign out the current user and delete their :::no-loc(cookie):::, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="facf6-165">次のコードは、 `Logout` *インデックス* ページにページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="facf6-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="facf6-166">への呼び出しで認証スキームが指定されていないことに注意して `SignOutAsync` ください。</span><span class="sxs-lookup"><span data-stu-id="facf6-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="facf6-167">`DefaultScheme`のアプリ `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` はフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="facf6-167">The app's `DefaultScheme` of `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="facf6-168">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="facf6-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
