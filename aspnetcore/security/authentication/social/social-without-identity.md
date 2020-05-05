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
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>ASP.NET Core せずにソーシャルサインインプロバイダー認証を使用するIdentity

[Kirk Larkin](https://twitter.com/serpent5)と[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index>ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。 このトピックで説明する方法にはIdentity 、認証プロバイダーとしての ASP.NET Core が含まれています。

このサンプルでは、ASP.NET Core Identity**なしで**外部認証プロバイダーを使用する方法を示します。 これは、ASP.NET Core Identityのすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。

このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。 Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。 別の外部認証プロバイダーと統合するには、次のトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [その他のプロバイダー](xref:security/authentication/otherlogins)

## <a name="configuration"></a>構成

`ConfigureServices`メソッドで、 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>、 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>、の各<xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>メソッドを使用して、アプリの認証方式を構成します。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

を<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>呼び出すと、アプリの<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>が設定されます。 `DefaultScheme`は、次`HttpContext`の認証拡張メソッドによって使用される既定のスキームです。

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

アプリの`DefaultScheme`を[cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookie") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。 アプリの<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に`ChallengeAsync`対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。 `DefaultChallengeScheme`を`DefaultScheme`オーバーライドします。 設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>時にオーバーライド`DefaultScheme`される追加のプロパティについては、「」を参照してください。

`Startup.Configure` `UseRouting`で、とを呼び出してから`UseEndpoints`を呼び出します。 `UseAuthentication` `UseAuthorization` これにより`HttpContext.User` 、プロパティが設定され、要求の承認ミドルウェアが実行されます。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。 Cookie 認証の詳細については<xref:security/authentication/cookie>、「」を参照してください。

## <a name="apply-authorization"></a>承認の適用

コントローラー、アクション、またはページに`AuthorizeAttribute`属性を適用して、アプリの認証構成をテストします。 次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>サインアウトする

現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。 次のコードは、 `Logout` *インデックス*ページにページハンドラーを追加します。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

へ`SignOutAsync`の呼び出しで認証スキームが指定されていないことに注意してください。 `DefaultScheme`の`CookieAuthenticationDefaults.AuthenticationScheme`アプリはフォールバックとして使用されます。

## <a name="additional-resources"></a>その他のリソース

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index>ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。 このトピックで説明する方法にはIdentity 、認証プロバイダーとしての ASP.NET Core が含まれています。

このサンプルでは、ASP.NET Core Identity**なしで**外部認証プロバイダーを使用する方法を示します。 これは、ASP.NET Core Identityのすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。

このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。 Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。 別の外部認証プロバイダーと統合するには、次のトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [その他のプロバイダー](xref:security/authentication/otherlogins)

## <a name="configuration"></a>構成

`ConfigureServices`メソッドで、 `AddAuthentication`、 `AddCookie`、の各`AddGoogle`メソッドを使用して、アプリの認証方式を構成します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)を呼び出すと、アプリの[defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)が設定されます。 `DefaultScheme`は、次`HttpContext`の認証拡張メソッドによって使用される既定のスキームです。

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

アプリの`DefaultScheme`を[cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookie") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。 アプリの<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に`ChallengeAsync`対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。 `DefaultChallengeScheme`を`DefaultScheme`オーバーライドします。 設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>時にオーバーライド`DefaultScheme`される追加のプロパティについては、「」を参照してください。

`Configure`メソッドで、 `UseAuthentication`メソッドを呼び出して、 `HttpContext.User`プロパティを設定する認証ミドルウェアを呼び出します。 または`UseAuthentication` `UseMvcWithDefaultRoute` `UseMvc`を呼び出す前に、メソッドを呼び出します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。 Cookie 認証の詳細については<xref:security/authentication/cookie>、「」を参照してください。

## <a name="apply-authorization"></a>承認の適用

コントローラー、アクション、またはページに`AuthorizeAttribute`属性を適用して、アプリの認証構成をテストします。 次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>サインアウトする

現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。 次のコードは、 `Logout` *インデックス*ページにページハンドラーを追加します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

へ`SignOutAsync`の呼び出しで認証スキームが指定されていないことに注意してください。 `DefaultScheme`の`CookieAuthenticationDefaults.AuthenticationScheme`アプリはフォールバックとして使用されます。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
