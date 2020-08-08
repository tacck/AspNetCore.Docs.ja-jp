---
title: ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する
author: rick-anderson
description: 外部プロバイダーから追加の要求とトークンを確立する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: f7a440a13891cd51226cad12924cfc65684632ea
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020185"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。 各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>必須コンポーネント

アプリでサポートする外部認証プロバイダーを決定します。 各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。 詳細については、「<xref:security/authentication/social/index>」を参照してください。 このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。

## <a name="set-the-client-id-and-client-secret"></a>クライアント ID とクライアントシークレットを設定する

OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。 アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。 アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。 詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Google での認証](xref:security/authentication/google-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [他の認証プロバイダー](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>認証スコープを確立する

を指定して、プロバイダーから取得するアクセス許可の一覧を指定し <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> ます。 共通外部プロバイダーの認証スコープは、次の表に表示されます。

| プロバイダー  | Scope                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

サンプルアプリで `userinfo.profile` <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> は、がで呼び出されたときに、Google のスコープがフレームワークによって自動的に追加され <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> ます。 アプリに追加のスコープが必要な場合は、それらをオプションに追加します。 次の例で `https://www.googleapis.com/auth/user.birthday.read` は、ユーザーの誕生日を取得するために Google スコープが追加されています。

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>ユーザーデータキーをマップして要求を作成する

プロバイダーのオプションで、 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。 要求の種類の詳細については、「」を参照してください <xref:System.Security.Claims.ClaimTypes> 。

このサンプルアプリでは、 `urn:google:locale` `urn:google:picture` `locale` Google ユーザーデータのキーとキーから、ロケール () と画像 () の要求を作成し `picture` ます。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

では、 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) がを使用してアプリにサインイン <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> します。 サインインプロセス中に、で <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 使用可能なユーザーデータの要求を格納でき <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> ます。

サンプルアプリでは、 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*) によって、 `urn:google:locale` `urn:google:picture` `ApplicationUser` 次の要求を含む、サインインしたのロケール () と画像 () の要求が確立 <xref:System.Security.Claims.ClaimTypes.GivenName> されます。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

既定では、ユーザーの要求は認証に格納され cookie ます。 認証 cookie が大きすぎると、次の理由により、アプリでエラーが発生する可能性があります。

* ブラウザーは、 cookie ヘッダーが長すぎることを検出します。
* 要求の全体的なサイズが大きすぎます。

ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。

* 要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。
* 認証ミドルウェアのカスタムを使用し <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> て、 Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 要求間で id を格納します。 小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。

## <a name="save-the-access-token"></a>アクセストークンを保存する

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>承認が成功した後に、アクセストークンと更新トークンをに格納するかどうかを定義し <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ます。 `SaveTokens`は `false` 、最終的な認証のサイズを小さくするために、既定でに設定され cookie ます。

サンプルアプリでは、の値をに設定し `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ます。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

を実行するときに、 `OnPostConfirmationAsync` の外部プロバイダーからアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納 `ApplicationUser` `AuthenticationProperties` します。

サンプルアプリでは、アクセストークン `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録されたユーザー) を*Account/externallogin. cshtml. cs*に保存します。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>カスタムトークンを追加する方法

の一部として格納されているカスタムトークンを追加する方法を示すために、 `SaveTokens` サンプルアプリでは、の AuthenticationToken.Name の現在のを使用してを追加し <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` ます。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>要求の作成と追加

フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。 詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。

ユーザーは、から派生し、抽象メソッドを実装することによって、カスタムアクションを定義でき <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> ます。

詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。

## <a name="removal-of-claim-actions-and-claims"></a>要求アクションと要求の削除

[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定されたのすべての要求アクションを <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> コレクションから削除します。 [DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、指定されたの要求を <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> id から削除します。 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>は、主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。

## <a name="sample-app-output"></a>サンプルアプリの出力

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。 各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>必須コンポーネント

アプリでサポートする外部認証プロバイダーを決定します。 各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。 詳細については、「<xref:security/authentication/social/index>」を参照してください。 このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。

## <a name="set-the-client-id-and-client-secret"></a>クライアント ID とクライアントシークレットを設定する

OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。 アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。 アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。 詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Google での認証](xref:security/authentication/google-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [他の認証プロバイダー](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>認証スコープを確立する

を指定して、プロバイダーから取得するアクセス許可の一覧を指定し <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> ます。 共通外部プロバイダーの認証スコープは、次の表に表示されます。

| プロバイダー  | Scope                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

サンプルアプリで `userinfo.profile` <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> は、がで呼び出されたときに、Google のスコープがフレームワークによって自動的に追加され <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> ます。 アプリに追加のスコープが必要な場合は、それらをオプションに追加します。 次の例で `https://www.googleapis.com/auth/user.birthday.read` は、ユーザーの誕生日を取得するために Google スコープが追加されています。

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>ユーザーデータキーをマップして要求を作成する

プロバイダーのオプションで、 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。 要求の種類の詳細については、「」を参照してください <xref:System.Security.Claims.ClaimTypes> 。

このサンプルアプリでは、 `urn:google:locale` `urn:google:picture` `locale` Google ユーザーデータのキーとキーから、ロケール () と画像 () の要求を作成し `picture` ます。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

では、 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) がを使用してアプリにサインイン <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> します。 サインインプロセス中に、で <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 使用可能なユーザーデータの要求を格納でき <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> ます。

サンプルアプリでは、 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*) によって、 `urn:google:locale` `urn:google:picture` `ApplicationUser` 次の要求を含む、サインインしたのロケール () と画像 () の要求が確立 <xref:System.Security.Claims.ClaimTypes.GivenName> されます。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

既定では、ユーザーの要求は認証に格納され cookie ます。 認証 cookie が大きすぎると、次の理由により、アプリでエラーが発生する可能性があります。

* ブラウザーは、 cookie ヘッダーが長すぎることを検出します。
* 要求の全体的なサイズが大きすぎます。

ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。

* 要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。
* 認証ミドルウェアのカスタムを使用し <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> て、 Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 要求間で id を格納します。 小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。

## <a name="save-the-access-token"></a>アクセストークンを保存する

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>承認が成功した後に、アクセストークンと更新トークンをに格納するかどうかを定義し <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ます。 `SaveTokens`は `false` 、最終的な認証のサイズを小さくするために、既定でに設定され cookie ます。

サンプルアプリでは、の値をに設定し `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ます。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

を実行するときに、 `OnPostConfirmationAsync` の外部プロバイダーからアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納 `ApplicationUser` `AuthenticationProperties` します。

サンプルアプリでは、アクセストークン `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録されたユーザー) を*Account/externallogin. cshtml. cs*に保存します。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>カスタムトークンを追加する方法

の一部として格納されているカスタムトークンを追加する方法を示すために、 `SaveTokens` サンプルアプリでは、の AuthenticationToken.Name の現在のを使用してを追加し <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` ます。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>要求の作成と追加

フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。 詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。

ユーザーは、から派生し、抽象メソッドを実装することによって、カスタムアクションを定義でき <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> ます。

詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。

## <a name="removal-of-claim-actions-and-claims"></a>要求アクションと要求の削除

[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定されたのすべての要求アクションを <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> コレクションから削除します。 [DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、指定されたの要求を <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> id から削除します。 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>は、主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。

## <a name="sample-app-output"></a>サンプルアプリの出力

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* [dotnet/AspNetCore engineering の社会 Alsample アプリ](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): リンクされたサンプルアプリは、 [Dotnet/AspNetCore GitHub リポジトリの](https://github.com/dotnet/AspNetCore) `master` エンジニアリングブランチにあります。 ブランチには、 `master` ASP.NET Core の次のリリースでアクティブな開発のコードが含まれています。 リリースされたバージョンの ASP.NET Core 用のサンプルアプリのバージョンを表示するには、[**ブランチ**] ドロップダウンリストを使用してリリースブランチを選択します (たとえば、 `release/{X.Y}` )。
