---
title: ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する
author: rick-anderson
description: 外部プロバイダーから追加の要求とトークンを確立する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 4503291ff887f79b1ad6eacd4e56943ce23335bc
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141509"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="39e4e-103">ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する</span><span class="sxs-lookup"><span data-stu-id="39e4e-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="39e4e-104">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="39e4e-105">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="39e4e-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="39e4e-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="39e4e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="39e4e-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="39e4e-107">Prerequisites</span></span>

<span data-ttu-id="39e4e-108">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="39e4e-109">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="39e4e-110">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39e4e-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="39e4e-111">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="39e4e-112">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="39e4e-112">Set the client ID and client secret</span></span>

<span data-ttu-id="39e4e-113">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="39e4e-114">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="39e4e-115">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="39e4e-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="39e4e-116">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="39e4e-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="39e4e-117">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="39e4e-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="39e4e-118">Google での認証</span><span class="sxs-lookup"><span data-stu-id="39e4e-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="39e4e-119">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="39e4e-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="39e4e-120">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="39e4e-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="39e4e-121">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="39e4e-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="39e4e-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="39e4e-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="39e4e-123">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="39e4e-124">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="39e4e-124">Establish the authentication scope</span></span>

<span data-ttu-id="39e4e-125">を指定して、プロバイダーから取得するアクセス許可の一覧を指定し <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="39e4e-126">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="39e4e-127">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="39e4e-127">Provider</span></span>  | <span data-ttu-id="39e4e-128">スコープ</span><span class="sxs-lookup"><span data-stu-id="39e4e-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="39e4e-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="39e4e-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="39e4e-130">Google</span><span class="sxs-lookup"><span data-stu-id="39e4e-130">Google</span></span>    | <span data-ttu-id="39e4e-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="39e4e-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="39e4e-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="39e4e-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="39e4e-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="39e4e-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="39e4e-134">サンプルアプリで `profile` は、がで呼び出されると、Google の、、の各 `email` `openid` スコープがフレームワークによって自動的に追加され <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="39e4e-135">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="39e4e-136">次の例で `https://www.googleapis.com/auth/user.birthday.read` は、ユーザーの誕生日を取得するために Google スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="39e4e-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="39e4e-137">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="39e4e-137">Map user data keys and create claims</span></span>

<span data-ttu-id="39e4e-138">プロバイダーのオプションで、 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="39e4e-139">要求の種類の詳細については、「」を参照してください <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="39e4e-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="39e4e-140">このサンプルアプリでは、 `urn:google:locale` `urn:google:picture` `locale` Google ユーザーデータのキーとキーから、ロケール () と画像 () の要求を作成し `picture` ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="39e4e-141">では、 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) がを使用してアプリにサインイン <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="39e4e-142">サインインプロセス中に、で <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 使用可能なユーザーデータの要求を格納でき <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="39e4e-143">サンプルアプリでは、 `OnPostConfirmationAsync` ( *Account/externallogin. cshtml* ) によって、 `urn:google:locale` `urn:google:picture` `ApplicationUser` 次の要求を含む、サインインしたのロケール () と画像 () の要求が確立 <xref:System.Security.Claims.ClaimTypes.GivenName> されます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-143">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="39e4e-144">既定では、ユーザーの要求は認証に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="39e4e-145">認証 cookie が大きすぎると、次の理由により、アプリでエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="39e4e-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="39e4e-146">ブラウザーは、 cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="39e4e-147">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="39e4e-148">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="39e4e-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="39e4e-149">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="39e4e-150">認証ミドルウェアのカスタムを使用し <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> て、 Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="39e4e-151">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="39e4e-152">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="39e4e-152">Save the access token</span></span>

<span data-ttu-id="39e4e-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 承認が成功した後に、アクセストークンと更新トークンをに格納するかどうかを定義し <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="39e4e-154">`SaveTokens` は `false` 、最終的な認証のサイズを小さくするために、既定でに設定され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="39e4e-155">サンプルアプリでは、の値をに設定し `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="39e4e-156">を実行するときに、 `OnPostConfirmationAsync` の外部プロバイダーからアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納 `ApplicationUser` `AuthenticationProperties` します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="39e4e-157">サンプルアプリでは、アクセストークン `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録されたユーザー) を *Account/externallogin. cshtml. cs* に保存します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="39e4e-158">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="39e4e-158">How to add additional custom tokens</span></span>

<span data-ttu-id="39e4e-159">の一部として格納されているカスタムトークンを追加する方法を示すために、 `SaveTokens` サンプルアプリでは、の AuthenticationToken.Name の現在のを使用してを追加し <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="39e4e-160">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="39e4e-160">Creating and adding claims</span></span>

<span data-ttu-id="39e4e-161">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="39e4e-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="39e4e-162">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39e4e-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="39e4e-163">ユーザーは、から派生し、抽象メソッドを実装することによって、カスタムアクションを定義でき <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="39e4e-164">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39e4e-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="39e4e-165">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="39e4e-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="39e4e-166">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) は、指定されたのすべての要求アクションを <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> コレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="39e4e-167">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) は、指定されたの要求を <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> id から削除します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="39e4e-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は、主に [OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="39e4e-169">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="39e4e-169">Sample app output</span></span>

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

<span data-ttu-id="39e4e-170">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="39e4e-171">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="39e4e-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="39e4e-172">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="39e4e-172">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="39e4e-173">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="39e4e-173">Prerequisites</span></span>

<span data-ttu-id="39e4e-174">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="39e4e-175">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="39e4e-176">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39e4e-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="39e4e-177">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="39e4e-178">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="39e4e-178">Set the client ID and client secret</span></span>

<span data-ttu-id="39e4e-179">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="39e4e-180">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="39e4e-181">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="39e4e-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="39e4e-182">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="39e4e-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="39e4e-183">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="39e4e-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="39e4e-184">Google での認証</span><span class="sxs-lookup"><span data-stu-id="39e4e-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="39e4e-185">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="39e4e-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="39e4e-186">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="39e4e-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="39e4e-187">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="39e4e-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="39e4e-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="39e4e-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="39e4e-189">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="39e4e-190">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="39e4e-190">Establish the authentication scope</span></span>

<span data-ttu-id="39e4e-191">を指定して、プロバイダーから取得するアクセス許可の一覧を指定し <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="39e4e-192">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="39e4e-193">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="39e4e-193">Provider</span></span>  | <span data-ttu-id="39e4e-194">スコープ</span><span class="sxs-lookup"><span data-stu-id="39e4e-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="39e4e-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="39e4e-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="39e4e-196">Google</span><span class="sxs-lookup"><span data-stu-id="39e4e-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="39e4e-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="39e4e-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="39e4e-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="39e4e-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="39e4e-199">サンプルアプリで `userinfo.profile` <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> は、がで呼び出されたときに、Google のスコープがフレームワークによって自動的に追加され <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="39e4e-200">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="39e4e-201">次の例で `https://www.googleapis.com/auth/user.birthday.read` は、ユーザーの誕生日を取得するために Google スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="39e4e-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="39e4e-202">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="39e4e-202">Map user data keys and create claims</span></span>

<span data-ttu-id="39e4e-203">プロバイダーのオプションで、 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="39e4e-204">要求の種類の詳細については、「」を参照してください <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="39e4e-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="39e4e-205">このサンプルアプリでは、 `urn:google:locale` `urn:google:picture` `locale` Google ユーザーデータのキーとキーから、ロケール () と画像 () の要求を作成し `picture` ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="39e4e-206">では、 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) がを使用してアプリにサインイン <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-206">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="39e4e-207">サインインプロセス中に、で <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 使用可能なユーザーデータの要求を格納でき <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="39e4e-208">サンプルアプリでは、 `OnPostConfirmationAsync` ( *Account/externallogin. cshtml* ) によって、 `urn:google:locale` `urn:google:picture` `ApplicationUser` 次の要求を含む、サインインしたのロケール () と画像 () の要求が確立 <xref:System.Security.Claims.ClaimTypes.GivenName> されます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-208">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="39e4e-209">既定では、ユーザーの要求は認証に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="39e4e-210">認証 cookie が大きすぎると、次の理由により、アプリでエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="39e4e-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="39e4e-211">ブラウザーは、 cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="39e4e-212">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="39e4e-213">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="39e4e-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="39e4e-214">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="39e4e-215">認証ミドルウェアのカスタムを使用し <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> て、 Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="39e4e-216">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="39e4e-217">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="39e4e-217">Save the access token</span></span>

<span data-ttu-id="39e4e-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 承認が成功した後に、アクセストークンと更新トークンをに格納するかどうかを定義し <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="39e4e-219">`SaveTokens` は `false` 、最終的な認証のサイズを小さくするために、既定でに設定され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="39e4e-220">サンプルアプリでは、の値をに設定し `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="39e4e-221">を実行するときに、 `OnPostConfirmationAsync` の外部プロバイダーからアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納 `ApplicationUser` `AuthenticationProperties` します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="39e4e-222">サンプルアプリでは、アクセストークン `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録されたユーザー) を *Account/externallogin. cshtml. cs* に保存します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="39e4e-223">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="39e4e-223">How to add additional custom tokens</span></span>

<span data-ttu-id="39e4e-224">の一部として格納されているカスタムトークンを追加する方法を示すために、 `SaveTokens` サンプルアプリでは、の AuthenticationToken.Name の現在のを使用してを追加し <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="39e4e-225">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="39e4e-225">Creating and adding claims</span></span>

<span data-ttu-id="39e4e-226">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="39e4e-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="39e4e-227">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39e4e-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="39e4e-228">ユーザーは、から派生し、抽象メソッドを実装することによって、カスタムアクションを定義でき <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> ます。</span><span class="sxs-lookup"><span data-stu-id="39e4e-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="39e4e-229">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39e4e-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="39e4e-230">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="39e4e-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="39e4e-231">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) は、指定されたのすべての要求アクションを <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> コレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="39e4e-232">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) は、指定されたの要求を <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> id から削除します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="39e4e-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は、主に [OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="39e4e-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="39e4e-234">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="39e4e-234">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="39e4e-235">その他の資料</span><span class="sxs-lookup"><span data-stu-id="39e4e-235">Additional resources</span></span>

* <span data-ttu-id="39e4e-236">[dotnet/AspNetCore engineering の社会 Alsample アプリ](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): リンクされたサンプルアプリは、 [Dotnet/AspNetCore GitHub リポジトリの](https://github.com/dotnet/AspNetCore) `master` エンジニアリングブランチにあります。</span><span class="sxs-lookup"><span data-stu-id="39e4e-236">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="39e4e-237">ブランチには、 `master` ASP.NET Core の次のリリースでアクティブな開発のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="39e4e-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="39e4e-238">リリースされたバージョンの ASP.NET Core 用のサンプルアプリのバージョンを表示するには、[ **ブランチ** ] ドロップダウンリストを使用してリリースブランチを選択します (たとえば、 `release/{X.Y}` )。</span><span class="sxs-lookup"><span data-stu-id="39e4e-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
