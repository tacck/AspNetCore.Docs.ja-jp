---
title: ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する
author: rick-anderson
description: 外部プロバイダーから追加の要求とトークンを確立する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 6acc1d78bf5cc39fd69329bad1cff0fbe52d9358
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769031"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="2d825-103">ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する</span><span class="sxs-lookup"><span data-stu-id="2d825-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2d825-104">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="2d825-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="2d825-105">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="2d825-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="2d825-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2d825-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2d825-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2d825-107">Prerequisites</span></span>

<span data-ttu-id="2d825-108">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="2d825-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="2d825-109">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="2d825-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="2d825-110">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="2d825-111">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="2d825-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="2d825-112">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="2d825-112">Set the client ID and client secret</span></span>

<span data-ttu-id="2d825-113">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="2d825-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="2d825-114">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="2d825-115">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d825-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="2d825-116">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="2d825-117">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="2d825-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="2d825-118">Google での認証</span><span class="sxs-lookup"><span data-stu-id="2d825-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="2d825-119">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="2d825-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="2d825-120">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="2d825-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="2d825-121">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="2d825-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="2d825-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="2d825-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="2d825-123">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="2d825-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="2d825-124">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="2d825-124">Establish the authentication scope</span></span>

<span data-ttu-id="2d825-125">を指定して、 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>プロバイダーから取得するアクセス許可の一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="2d825-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="2d825-126">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="2d825-127">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="2d825-127">Provider</span></span>  | <span data-ttu-id="2d825-128">Scope</span><span class="sxs-lookup"><span data-stu-id="2d825-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="2d825-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="2d825-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="2d825-130">Google</span><span class="sxs-lookup"><span data-stu-id="2d825-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="2d825-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="2d825-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="2d825-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="2d825-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="2d825-133">サンプルアプリでは、 `userinfo.profile` <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>がで呼び出されたときに、Google のスコープがフレームワークによって自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="2d825-134">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="2d825-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="2d825-135">次の例では、ユーザー `https://www.googleapis.com/auth/user.birthday.read`の誕生日を取得するために Google スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="2d825-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="2d825-136">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="2d825-136">Map user data keys and create claims</span></span>

<span data-ttu-id="2d825-137">プロバイダーのオプションで、サインイン時に<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*>読み取る<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*>アプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。</span><span class="sxs-lookup"><span data-stu-id="2d825-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="2d825-138">要求の種類の詳細について<xref:System.Security.Claims.ClaimTypes>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="2d825-139">このサンプルアプリでは、`urn:google:locale`Google ユーザーデータの`urn:google:picture`キー `locale`と`picture`キーから、ロケール () と画像 () の要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="2d825-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="2d825-140">で`Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`は、 <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) がを使用して<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>アプリにサインインします。</span><span class="sxs-lookup"><span data-stu-id="2d825-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="2d825-141">サインインプロセス中に、で使用<xref:Microsoft.AspNetCore.Identity.UserManager%601>可能なユーザー `ApplicationUser`データの要求を格納でき<xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>ます。</span><span class="sxs-lookup"><span data-stu-id="2d825-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="2d825-142">サンプルアプリでは、 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*) によって、次の`urn:google:locale` <xref:System.Security.Claims.ClaimTypes.GivenName>要求を含む、`urn:google:picture`サインイン`ApplicationUser`したのロケール () と画像 () の要求が確立されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="2d825-143">既定では、ユーザーの要求は認証 cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-143">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="2d825-144">認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2d825-144">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="2d825-145">ブラウザーは cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="2d825-145">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="2d825-146">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="2d825-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="2d825-147">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2d825-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="2d825-148">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="2d825-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="2d825-149">Cookie 認証ミドルウェア<xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore>のカスタムを使用して、要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="2d825-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="2d825-150">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="2d825-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="2d825-151">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="2d825-151">Save the access token</span></span>

<span data-ttu-id="2d825-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>承認が成功した後に、アクセストークンと<xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties>更新トークンをに格納するかどうかを定義します。</span><span class="sxs-lookup"><span data-stu-id="2d825-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="2d825-153">`SaveTokens`は、最終的`false`な認証クッキーのサイズを小さくするために、既定でに設定されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="2d825-154">サンプルアプリで`SaveTokens` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>は、の値を`true`に設定します。</span><span class="sxs-lookup"><span data-stu-id="2d825-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="2d825-155">を`OnPostConfirmationAsync`実行するときに、 `ApplicationUser`の`AuthenticationProperties`外部プロバイダーからアクセストークン ([externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納します。</span><span class="sxs-lookup"><span data-stu-id="2d825-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="2d825-156">サンプルアプリでは、アクセストークン`OnPostConfirmationAsync` (新しいユーザー登録) と`OnGetCallbackAsync` (以前に登録されたユーザー) を*Account/externallogin. cshtml. cs*に保存します。</span><span class="sxs-lookup"><span data-stu-id="2d825-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="2d825-157">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="2d825-157">How to add additional custom tokens</span></span>

<span data-ttu-id="2d825-158">の`SaveTokens`一部として格納されているカスタムトークンを追加する方法を示すために、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>サンプルアプリで<xref:System.DateTime>は、 [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)の`TicketCreated`AuthenticationToken.Name の現在のを使用してを追加します。</span><span class="sxs-lookup"><span data-stu-id="2d825-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="2d825-159">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="2d825-159">Creating and adding claims</span></span>

<span data-ttu-id="2d825-160">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2d825-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="2d825-161">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="2d825-162">ユーザーは、から<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction>派生し、抽象<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>メソッドを実装することによって、カスタムアクションを定義できます。</span><span class="sxs-lookup"><span data-stu-id="2d825-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="2d825-163">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="2d825-164">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="2d825-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="2d825-165">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定さ<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>れたのすべての要求アクションをコレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="2d825-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="2d825-166">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、指定さ<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>れたの要求を id から削除します。</span><span class="sxs-lookup"><span data-stu-id="2d825-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="2d825-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>は、主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="2d825-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="2d825-168">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="2d825-168">Sample app output</span></span>

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

<span data-ttu-id="2d825-169">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="2d825-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="2d825-170">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="2d825-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="2d825-171">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2d825-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2d825-172">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2d825-172">Prerequisites</span></span>

<span data-ttu-id="2d825-173">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="2d825-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="2d825-174">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="2d825-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="2d825-175">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="2d825-176">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="2d825-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="2d825-177">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="2d825-177">Set the client ID and client secret</span></span>

<span data-ttu-id="2d825-178">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="2d825-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="2d825-179">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="2d825-180">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d825-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="2d825-181">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="2d825-182">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="2d825-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="2d825-183">Google での認証</span><span class="sxs-lookup"><span data-stu-id="2d825-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="2d825-184">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="2d825-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="2d825-185">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="2d825-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="2d825-186">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="2d825-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="2d825-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="2d825-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="2d825-188">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="2d825-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="2d825-189">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="2d825-189">Establish the authentication scope</span></span>

<span data-ttu-id="2d825-190">を指定して、 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>プロバイダーから取得するアクセス許可の一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="2d825-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="2d825-191">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="2d825-192">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="2d825-192">Provider</span></span>  | <span data-ttu-id="2d825-193">Scope</span><span class="sxs-lookup"><span data-stu-id="2d825-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="2d825-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="2d825-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="2d825-195">Google</span><span class="sxs-lookup"><span data-stu-id="2d825-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="2d825-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="2d825-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="2d825-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="2d825-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="2d825-198">サンプルアプリでは、 `userinfo.profile` <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>がで呼び出されたときに、Google のスコープがフレームワークによって自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="2d825-199">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="2d825-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="2d825-200">次の例では、ユーザー `https://www.googleapis.com/auth/user.birthday.read`の誕生日を取得するために Google スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="2d825-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="2d825-201">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="2d825-201">Map user data keys and create claims</span></span>

<span data-ttu-id="2d825-202">プロバイダーのオプションで、サインイン時に<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*>読み取る<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*>アプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。</span><span class="sxs-lookup"><span data-stu-id="2d825-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="2d825-203">要求の種類の詳細について<xref:System.Security.Claims.ClaimTypes>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="2d825-204">このサンプルアプリでは、`urn:google:locale`Google ユーザーデータの`urn:google:picture`キー `locale`と`picture`キーから、ロケール () と画像 () の要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="2d825-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="2d825-205">で`Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`は、 <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) がを使用して<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>アプリにサインインします。</span><span class="sxs-lookup"><span data-stu-id="2d825-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="2d825-206">サインインプロセス中に、で使用<xref:Microsoft.AspNetCore.Identity.UserManager%601>可能なユーザー `ApplicationUser`データの要求を格納でき<xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>ます。</span><span class="sxs-lookup"><span data-stu-id="2d825-206">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="2d825-207">サンプルアプリでは、 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*) によって、次の`urn:google:locale` <xref:System.Security.Claims.ClaimTypes.GivenName>要求を含む、`urn:google:picture`サインイン`ApplicationUser`したのロケール () と画像 () の要求が確立されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-207">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="2d825-208">既定では、ユーザーの要求は認証 cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-208">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="2d825-209">認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2d825-209">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="2d825-210">ブラウザーは cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="2d825-210">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="2d825-211">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="2d825-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="2d825-212">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2d825-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="2d825-213">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="2d825-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="2d825-214">Cookie 認証ミドルウェア<xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore>のカスタムを使用して、要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="2d825-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="2d825-215">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="2d825-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="2d825-216">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="2d825-216">Save the access token</span></span>

<span data-ttu-id="2d825-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>承認が成功した後に、アクセストークンと<xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties>更新トークンをに格納するかどうかを定義します。</span><span class="sxs-lookup"><span data-stu-id="2d825-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="2d825-218">`SaveTokens`は、最終的`false`な認証クッキーのサイズを小さくするために、既定でに設定されます。</span><span class="sxs-lookup"><span data-stu-id="2d825-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="2d825-219">サンプルアプリで`SaveTokens` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>は、の値を`true`に設定します。</span><span class="sxs-lookup"><span data-stu-id="2d825-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="2d825-220">を`OnPostConfirmationAsync`実行するときに、 `ApplicationUser`の`AuthenticationProperties`外部プロバイダーからアクセストークン ([externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納します。</span><span class="sxs-lookup"><span data-stu-id="2d825-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="2d825-221">サンプルアプリでは、アクセストークン`OnPostConfirmationAsync` (新しいユーザー登録) と`OnGetCallbackAsync` (以前に登録されたユーザー) を*Account/externallogin. cshtml. cs*に保存します。</span><span class="sxs-lookup"><span data-stu-id="2d825-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="2d825-222">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="2d825-222">How to add additional custom tokens</span></span>

<span data-ttu-id="2d825-223">の`SaveTokens`一部として格納されているカスタムトークンを追加する方法を示すために、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>サンプルアプリで<xref:System.DateTime>は、 [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)の`TicketCreated`AuthenticationToken.Name の現在のを使用してを追加します。</span><span class="sxs-lookup"><span data-stu-id="2d825-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="2d825-224">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="2d825-224">Creating and adding claims</span></span>

<span data-ttu-id="2d825-225">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2d825-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="2d825-226">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="2d825-227">ユーザーは、から<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction>派生し、抽象<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>メソッドを実装することによって、カスタムアクションを定義できます。</span><span class="sxs-lookup"><span data-stu-id="2d825-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="2d825-228">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d825-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="2d825-229">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="2d825-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="2d825-230">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定さ<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>れたのすべての要求アクションをコレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="2d825-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="2d825-231">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、指定さ<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>れたの要求を id から削除します。</span><span class="sxs-lookup"><span data-stu-id="2d825-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="2d825-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>は、主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="2d825-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="2d825-233">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="2d825-233">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="2d825-234">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2d825-234">Additional resources</span></span>

* <span data-ttu-id="2d825-235">[dotnet/AspNetCore engineering の社会 alsample アプリ](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash;リンクされたサンプルアプリは、 [dotnet/AspNetCore GitHub リポジトリの](https://github.com/dotnet/AspNetCore) `master`エンジニアリングブランチにあります。</span><span class="sxs-lookup"><span data-stu-id="2d825-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="2d825-236">ブランチ`master`には、ASP.NET Core の次のリリースでアクティブな開発のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2d825-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="2d825-237">リリースされたバージョンの ASP.NET Core 用のサンプルアプリのバージョンを表示するには、[**ブランチ**] ドロップダウンリストを使用して`release/{X.Y}`リリースブランチを選択します (たとえば、)。</span><span class="sxs-lookup"><span data-stu-id="2d825-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
