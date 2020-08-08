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
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-no-locidentity"></a>ASP.NET Core せずにソーシャルサインインプロバイダー認証を使用するIdentity

[Kirk Larkin](https://twitter.com/serpent5)と[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index>ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。 このトピックで説明する方法には、 Identity 認証プロバイダーとしての ASP.NET Core が含まれています。

このサンプルでは、ASP.NET Core**なしで**外部認証プロバイダーを使用する方法を示し Identity ます。 これは、ASP.NET Core のすべての機能を必要としない Identity が、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。

このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。 Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。 別の外部認証プロバイダーと統合するには、次のトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [その他のプロバイダー](xref:security/authentication/otherlogins)

## <a name="configuration"></a>構成

メソッドで、 `ConfigureServices` 、、の各メソッドを使用して、アプリの認証方式を構成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> ます。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

を呼び出すと、 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> アプリのが設定され <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> ます。 `DefaultScheme`は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

アプリの `DefaultScheme` を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") は、 Cookie これらの拡張メソッドの既定のスキームとして s を使用するようにアプリを構成します。 アプリのを <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます `ChallengeAsync` 。 `DefaultChallengeScheme``DefaultScheme`をオーバーライドします。 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>設定時にオーバーライドされる追加のプロパティについては、「」を参照してください `DefaultScheme` 。

で、とを呼び出してから `Startup.Configure` `UseAuthentication` を呼び出し `UseAuthorization` `UseRouting` `UseEndpoints` ます。 これにより、プロパティが設定され、 `HttpContext.User` 要求の承認ミドルウェアが実行されます。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。 認証の詳細につい cookie ては、「」を参照してください <xref:security/authentication/cookie> 。

## <a name="apply-authorization"></a>承認の適用

`AuthorizeAttribute`コントローラー、アクション、またはページに属性を適用して、アプリの認証構成をテストします。 次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトし、を削除するに cookie は、 [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。 次のコードは、 `Logout` *インデックス*ページにページハンドラーを追加します。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

への呼び出しで認証スキームが指定されていないことに注意して `SignOutAsync` ください。 `DefaultScheme`のアプリ `CookieAuthenticationDefaults.AuthenticationScheme` はフォールバックとして使用されます。

## <a name="additional-resources"></a>その他のリソース

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index>ユーザーが外部認証プロバイダーからの資格情報を使用して OAuth 2.0 を使用してサインインできるようにする方法について説明します。 このトピックで説明する方法には、 Identity 認証プロバイダーとしての ASP.NET Core が含まれています。

このサンプルでは、ASP.NET Core**なしで**外部認証プロバイダーを使用する方法を示し Identity ます。 これは、ASP.NET Core のすべての機能を必要としない Identity が、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。

このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。 Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。 別の外部認証プロバイダーと統合するには、次のトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [その他のプロバイダー](xref:security/authentication/otherlogins)

## <a name="configuration"></a>構成

メソッドで、 `ConfigureServices` 、、の各メソッドを使用して、アプリの認証方式を構成し `AddAuthentication` `AddCookie` `AddGoogle` ます。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)を呼び出すと、アプリの[defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)が設定されます。 `DefaultScheme`は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

アプリの `DefaultScheme` を[ Cookie authenticationdefaults に設定します。 authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") は、 Cookie これらの拡張メソッドの既定のスキームとして s を使用するようにアプリを構成します。 アプリのを <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、に対する呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます `ChallengeAsync` 。 `DefaultChallengeScheme``DefaultScheme`をオーバーライドします。 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>設定時にオーバーライドされる追加のプロパティについては、「」を参照してください `DefaultScheme` 。

メソッドで、 `Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。 `UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。 認証の詳細につい cookie ては、「」を参照してください <xref:security/authentication/cookie> 。

## <a name="apply-authorization"></a>承認の適用

`AuthorizeAttribute`コントローラー、アクション、またはページに属性を適用して、アプリの認証構成をテストします。 次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトし、を削除するに cookie は、 [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。 次のコードは、 `Logout` *インデックス*ページにページハンドラーを追加します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

への呼び出しで認証スキームが指定されていないことに注意して `SignOutAsync` ください。 `DefaultScheme`のアプリ `CookieAuthenticationDefaults.AuthenticationScheme` はフォールバックとして使用されます。

## <a name="additional-resources"></a>その他のリソース

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
