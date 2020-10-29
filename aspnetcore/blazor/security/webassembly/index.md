---
title: 'ASP.NET Core :::no-loc(Blazor WebAssembly)::: をセキュリティで保護する'
author: guardrex
description: 'シングル ページ アプリケーション (SPA) として :::no-loc(Blazor)::: WebAssemlby アプリをセキュリティで保護する方法について説明します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: 2c160f21ccccb44f9047cf23c67bc191ad1b2b3d
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690376"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="3e8da-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: をセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="3e8da-103">Secure ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="3e8da-104">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="3e8da-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="3e8da-105">:::no-loc(Blazor WebAssembly)::: アプリは、シングル ページ アプリケーション (SPA) と同じ方法でセキュリティ保護されます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-105">:::no-loc(Blazor WebAssembly)::: apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="3e8da-106">ユーザーを SPA で認証する方法はいくつかありますが、最も一般的で包括的な方法は、[OpenID Connect (OIDC)](https://openid.net/connect/) などの [OAuth 2.0 プロトコル](https://oauth.net/)に基づく実装を使用することです。</span><span class="sxs-lookup"><span data-stu-id="3e8da-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="3e8da-107">認証ライブラリ</span><span class="sxs-lookup"><span data-stu-id="3e8da-107">Authentication library</span></span>

<span data-ttu-id="3e8da-108">:::no-loc(Blazor WebAssembly)::: では、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ライブラリを介して OIDC を使用したアプリの認証と承認がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3e8da-108">:::no-loc(Blazor WebAssembly)::: supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="3e8da-109">ライブラリには、ASP.NET Core バックエンドに対してシームレスに認証を行うための一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3e8da-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="3e8da-110">ライブラリによって :::no-loc(ASP.NET Core Identity)::: が、[:::no-loc(Identity)::: Server](https://identityserver.io/) 上に構築された API 認可サポートと統合されます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-110">The library integrates :::no-loc(ASP.NET Core Identity)::: with API authorization support built on top of [:::no-loc(Identity)::: Server](https://identityserver.io/).</span></span> <span data-ttu-id="3e8da-111">このライブラリは、OpenID Provider (OP) と呼ばれる OIDC をサポートするすべてのサードパーティ :::no-loc(Identity)::: プロバイダー (IP) に対して認証できます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-111">The library can authenticate against any third-party :::no-loc(Identity)::: Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="3e8da-112">:::no-loc(Blazor WebAssembly)::: の認証のサポートは、基になる認証プロトコルの詳細を処理するために使用される、`oidc-client.js` ライブラリに基づいて構築されています。</span><span class="sxs-lookup"><span data-stu-id="3e8da-112">The authentication support in :::no-loc(Blazor WebAssembly)::: is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="3e8da-113">SameSite :::no-loc(cookie)::: の使用など、SPA を認証するためのその他のオプションも存在します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-113">Other options for authenticating SPAs exist, such as the use of SameSite :::no-loc(cookie):::s.</span></span> <span data-ttu-id="3e8da-114">ただし、:::no-loc(Blazor WebAssembly)::: のエンジニアリング設計は、:::no-loc(Blazor WebAssembly)::: アプリでの認証に最適なオプションとして OAuth および OIDC にも採用されています。</span><span class="sxs-lookup"><span data-stu-id="3e8da-114">However, the engineering design of :::no-loc(Blazor WebAssembly)::: is settled on OAuth and OIDC as the best option for authentication in :::no-loc(Blazor WebAssembly)::: apps.</span></span> <span data-ttu-id="3e8da-115">[JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) に基づく[トークンベースの認証](xref:security/anti-request-forgery#token-based-authentication)は、機能とセキュリティ上の理由により、[:::no-loc(cookie)::: ベースの認証](xref:security/anti-request-forgery#:::no-loc(cookie):::-based-authentication)に基づいて選択されています。</span><span class="sxs-lookup"><span data-stu-id="3e8da-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [:::no-loc(cookie):::-based authentication](xref:security/anti-request-forgery#:::no-loc(cookie):::-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="3e8da-116">トークンベースのプロトコルを使用すると、トークンがすべての要求で送信されないため、攻撃対象領域が小さくなります。</span><span class="sxs-lookup"><span data-stu-id="3e8da-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="3e8da-117">サーバー エンドポイントでは、トークンが明示的に送信されるため、[クロスサイト リクエスト フォージェリ (CSRF)](xref:security/anti-request-forgery) に対する保護が必要ありません。</span><span class="sxs-lookup"><span data-stu-id="3e8da-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="3e8da-118">これにより、MVC または :::no-loc(Razor)::: ページ アプリと共に、:::no-loc(Blazor WebAssembly)::: アプリをホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-118">This allows you to host :::no-loc(Blazor WebAssembly)::: apps alongside MVC or :::no-loc(Razor)::: pages apps.</span></span>
* <span data-ttu-id="3e8da-119">トークンの権限は :::no-loc(cookie)::: よりも狭くなります。</span><span class="sxs-lookup"><span data-stu-id="3e8da-119">Tokens have narrower permissions than :::no-loc(cookie):::s.</span></span> <span data-ttu-id="3e8da-120">たとえば、該当する機能が明示的に実装されていない限り、トークンを使用してユーザー アカウントを管理したり、ユーザーのパスワードを変更したりできません。</span><span class="sxs-lookup"><span data-stu-id="3e8da-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="3e8da-121">トークンの有効期間は短く、既定では 1 時間です。これにより、攻撃時間が制限されます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="3e8da-122">トークンは、いつでも取り消すことができます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="3e8da-123">自己完結型の JWT は、クライアントとサーバーの認証プロセスを保証します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="3e8da-124">たとえば、クライアントには、受信したトークンが正当であり、指定した認証プロセスの一環として出力されたことを検出して検証する手段があります。</span><span class="sxs-lookup"><span data-stu-id="3e8da-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="3e8da-125">サード パーティが認証プロセスの途中でトークンを切り替えようとすると、クライアントは切り替えられたトークンを検出して使用しないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="3e8da-126">OAuth および OIDC を使用したトークンは、アプリが安全であることを確認するために、正しく動作しているユーザー エージェントに依存しません。</span><span class="sxs-lookup"><span data-stu-id="3e8da-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="3e8da-127">OAuth や OIDC などのトークンベースのプロトコルでは、同じセキュリティ特性のセットを使用して、ホストされたアプリとスタンドアロンのアプリの認証と認可を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="3e8da-128">OIDC を使用した認証プロセス</span><span class="sxs-lookup"><span data-stu-id="3e8da-128">Authentication process with OIDC</span></span>

<span data-ttu-id="3e8da-129">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ライブラリには、OIDC を使用した認証と認可を実装するためのいくつかのプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3e8da-129">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="3e8da-130">大まかにいうと、認証は次のようにして行われます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="3e8da-131">匿名ユーザーがログイン ボタンを選択する、または [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性が適用されたページを要求すると、そのユーザーがアプリのログイン ページ (`/authentication/login`) にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="3e8da-132">ログイン ページで、認証ライブラリが承認エンドポイントへのリダイレクトを準備します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="3e8da-133">承認エンドポイントは、:::no-loc(Blazor WebAssembly)::: アプリの外部にあり、別のオリジンでホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-133">The authorization endpoint is outside of the :::no-loc(Blazor WebAssembly)::: app and can be hosted at a separate origin.</span></span> <span data-ttu-id="3e8da-134">エンドポイントは、ユーザーが認証されているかどうかを判断し、応答として 1 つ以上のトークンを発行する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="3e8da-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="3e8da-135">認証ライブラリは、認証応答を受信するためのログイン コールバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="3e8da-136">ユーザーが認証されていない場合、そのユーザーは基になる認証システム (通常は :::no-loc(ASP.NET Core Identity):::) にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually :::no-loc(ASP.NET Core Identity):::.</span></span>
  * <span data-ttu-id="3e8da-137">ユーザーが既に認証されている場合、承認エンドポイントは適切なトークンを生成し、ブラウザーをログイン コールバック エンドポイント (`/authentication/login-callback`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="3e8da-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="3e8da-138">:::no-loc(Blazor WebAssembly)::: アプリでログイン コールバック エンドポイント (`/authentication/login-callback`) が読み込まれると、認証応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-138">When the :::no-loc(Blazor WebAssembly)::: app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="3e8da-139">認証プロセスが正常に完了した場合、ユーザーは認証され、必要に応じてユーザーが要求した元の保護された URL に戻されます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="3e8da-140">何らかの理由で認証プロセスが失敗した場合、ユーザーはログイン失敗ページ (`/authentication/login-failed`) に送信され、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="3e8da-141">`Authentication` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3e8da-141">`Authentication` component</span></span>

<span data-ttu-id="3e8da-142">`Authentication` コンポーネント (`Pages/Authentication.razor`) はリモート認証操作を処理し、次のことをアプリに許可します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-142">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="3e8da-143">認証状態のアプリのルートを構成する。</span><span class="sxs-lookup"><span data-stu-id="3e8da-143">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="3e8da-144">認証状態の UI コンテンツを設定する。</span><span class="sxs-lookup"><span data-stu-id="3e8da-144">Set UI content for authentication states.</span></span>
* <span data-ttu-id="3e8da-145">認証状態を管理する。</span><span class="sxs-lookup"><span data-stu-id="3e8da-145">Manage authentication state.</span></span>

<span data-ttu-id="3e8da-146">ユーザーの登録やサインインなどの認証アクションは、:::no-loc(Blazor)::: フレームワークの <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> コンポーネントに渡されます。ここでは認証操作全体で状態が保持され、制御されます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-146">Authentication actions, such as registering or signing in a user, are passed to the :::no-loc(Blazor)::: framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="3e8da-147">使用例を含む詳細については、「<xref:blazor/security/webassembly/additional-scenarios>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e8da-147">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="3e8da-148">承認</span><span class="sxs-lookup"><span data-stu-id="3e8da-148">Authorization</span></span>

<span data-ttu-id="3e8da-149">:::no-loc(Blazor WebAssembly)::: アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、承認チェックがバイパスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3e8da-149">In :::no-loc(Blazor WebAssembly)::: apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="3e8da-150">JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="3e8da-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="3e8da-151">**常に、クライアント側アプリからアクセスされるすべての API エンドポイント内のサーバー上で承認チェックを実行します。**</span><span class="sxs-lookup"><span data-stu-id="3e8da-151">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="3e8da-152">アプリ全体での承認を要求する</span><span class="sxs-lookup"><span data-stu-id="3e8da-152">Require authorization for the entire app</span></span>

<span data-ttu-id="3e8da-153">次のいずれかの方法を使用して、[`[Authorize]` 属性](xref:blazor/security/index#authorize-attribute) ([API ドキュメント](xref:System.Web.Mvc.AuthorizeAttribute)) をアプリの各 :::no-loc(Razor)::: コンポーネントに適用します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-153">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each :::no-loc(Razor)::: component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="3e8da-154">`_Imports.razor` ファイルの [`@attribute`](xref:mvc/views/razor#attribute) ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-154">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="3e8da-155">`Pages` フォルダー内の各 :::no-loc(Razor)::: コンポーネントに属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-155">Add the attribute to each :::no-loc(Razor)::: component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="3e8da-156"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> を持つポリシーに <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> を設定することはサポートされて **いません** 。</span><span class="sxs-lookup"><span data-stu-id="3e8da-156">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="3e8da-157">更新トークン</span><span class="sxs-lookup"><span data-stu-id="3e8da-157">Refresh tokens</span></span>

<span data-ttu-id="3e8da-158">更新トークンは、:::no-loc(Blazor WebAssembly)::: アプリのクライアント側でセキュリティで保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="3e8da-158">Refresh tokens can't be secured client-side in :::no-loc(Blazor WebAssembly)::: apps.</span></span> <span data-ttu-id="3e8da-159">そのため、更新トークンを直接使用するためにアプリに送信するべきではありません。</span><span class="sxs-lookup"><span data-stu-id="3e8da-159">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="3e8da-160">更新トークンは、ホストされている :::no-loc(Blazor WebAssembly)::: ソリューションのサーバー側アプリで、サード パーティの API にアクセスするために保持および使用することができます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-160">Refresh tokens can be maintained and used by the server-side app in a Hosted :::no-loc(Blazor WebAssembly)::: solution to access third-party APIs.</span></span> <span data-ttu-id="3e8da-161">詳細については、「<xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e8da-161">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="3e8da-162">ユーザーに対するクレームを確立する</span><span class="sxs-lookup"><span data-stu-id="3e8da-162">Establish claims for users</span></span>

<span data-ttu-id="3e8da-163">アプリでは、サーバーへの Web API 呼び出しに基づくユーザーへのクレームが必要になることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="3e8da-163">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="3e8da-164">たとえば、クレームは、アプリで[承認を確立する](xref:blazor/security/index#authorization)ためによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="3e8da-164">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="3e8da-165">このようなシナリオにおいて、アプリでは、サービスにアクセスするためのアクセス トークンを要求し、そのトークンを使用してクレームに対するユーザー データを取得します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-165">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="3e8da-166">例については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e8da-166">For examples, see the following resources:</span></span>

* [<span data-ttu-id="3e8da-167">その他のシナリオ: ユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="3e8da-167">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="implementation-guidance"></a><span data-ttu-id="3e8da-168">実装ガイダンス</span><span class="sxs-lookup"><span data-stu-id="3e8da-168">Implementation guidance</span></span>

<span data-ttu-id="3e8da-169">この「 *概要* 」の記事では、特定のプロバイダーに対して :::no-loc(Blazor WebAssembly)::: アプリのユーザーを認証する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3e8da-169">Articles under this *Overview* provide information on authenticating users in :::no-loc(Blazor WebAssembly)::: apps against specific providers.</span></span>

<span data-ttu-id="3e8da-170">スタンドアロン :::no-loc(Blazor WebAssembly)::: アプリ:</span><span class="sxs-lookup"><span data-stu-id="3e8da-170">Standalone :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* [<span data-ttu-id="3e8da-171">OIDC プロバイダーと WebAssembly 認証ライブラリ向けの一般的なガイダンス</span><span class="sxs-lookup"><span data-stu-id="3e8da-171">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="3e8da-172">Microsoft アカウント</span><span class="sxs-lookup"><span data-stu-id="3e8da-172">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="3e8da-173">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="3e8da-173">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="3e8da-174">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="3e8da-174">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="3e8da-175">ホストされている :::no-loc(Blazor WebAssembly)::: アプリ:</span><span class="sxs-lookup"><span data-stu-id="3e8da-175">Hosted :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* [<span data-ttu-id="3e8da-176">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="3e8da-176">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="3e8da-177">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="3e8da-177">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="3e8da-178">:::no-loc(Identity)::: サーバー</span><span class="sxs-lookup"><span data-stu-id="3e8da-178">:::no-loc(Identity)::: Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3e8da-179">構成に関するその他のガイダンスについては、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e8da-179">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3e8da-180">構成に関するその他のガイダンスについては、「<xref:blazor/security/webassembly/additional-scenarios>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3e8da-180">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end
