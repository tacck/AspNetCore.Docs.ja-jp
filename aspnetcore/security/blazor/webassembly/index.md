---
title: ASP.NET Core Blazor WebAssembly をセキュリティで保護する
author: guardrex
description: シングル ページ アプリケーション (SPA) として Blazor WebAssemlby アプリをセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: 153e88bb5bd99803f0ac4a0c50213e22a3278cad
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424527"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a><span data-ttu-id="e4948-103">ASP.NET Core Blazor WebAssembly をセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="e4948-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="e4948-104">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="e4948-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="e4948-105"> WebAssembly アプリは、シングル ページ アプリケーション (SPA) と同じ方法でセキュリティ保護されます。</span><span class="sxs-lookup"><span data-stu-id="e4948-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="e4948-106">ユーザーを SPA で認証する方法はいくつかありますが、最も一般的で包括的な方法は、[Open ID Connect (OIDC)](https://openid.net/connect/) などの [OAuth 2.0 プロトコル](https://oauth.net/)に基づく実装を使用することです。</span><span class="sxs-lookup"><span data-stu-id="e4948-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="e4948-107">認証ライブラリ</span><span class="sxs-lookup"><span data-stu-id="e4948-107">Authentication library</span></span>

Blazor<span data-ttu-id="e4948-108"> WebAssembly は、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリを介して OIDC を使用したアプリの認証と認可をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="e4948-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="e4948-109">ライブラリには、ASP.NET Core バックエンドに対してシームレスに認証を行うための一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="e4948-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="e4948-110">ライブラリによって ASP.NET Core Identity が、[Identity Server](https://identityserver.io/) 上に構築された API 認可サポートと統合されます。</span><span class="sxs-lookup"><span data-stu-id="e4948-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="e4948-111">このライブラリは、OpenID Provider (OP) と呼ばれる OIDC をサポートするすべてのサードパーティ Identity プロバイダー (IP) に対して認証できます。</span><span class="sxs-lookup"><span data-stu-id="e4948-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="e4948-112">Blazor WebAssembly の認証のサポートは、基になる認証プロトコルの詳細を処理するために使用される、*oidc-client.js* ライブラリの上に構築されています。</span><span class="sxs-lookup"><span data-stu-id="e4948-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="e4948-113">SameSite Cookie の使用など、SPA を認証するためのその他のオプションも存在します。</span><span class="sxs-lookup"><span data-stu-id="e4948-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="e4948-114">ただし、Blazor WebAssembly のエンジニアリング設計は、Blazor WebAssembly アプリでの認証に最適なオプションとして OAuth および OIDC にも採用されています。</span><span class="sxs-lookup"><span data-stu-id="e4948-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="e4948-115">[JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) に基づく[トークンベースの認証](xref:security/anti-request-forgery#token-based-authentication)は、機能とセキュリティ上の理由により、[Cookie ベースの認証](xref:security/anti-request-forgery#cookie-based-authentication) に基づいて選択されています。</span><span class="sxs-lookup"><span data-stu-id="e4948-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="e4948-116">トークンベースのプロトコルを使用すると、トークンがすべての要求で送信されないため、攻撃対象領域が小さくなります。</span><span class="sxs-lookup"><span data-stu-id="e4948-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="e4948-117">サーバー エンドポイントでは、トークンが明示的に送信されるため、[クロスサイト リクエスト フォージェリ (CSRF)](xref:security/anti-request-forgery) に対する保護が必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e4948-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="e4948-118">これにより、MVC または Razor Pages アプリと共に、Blazor WebAssembly アプリをホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="e4948-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="e4948-119">トークンの権限は Cookie よりも狭くなります。</span><span class="sxs-lookup"><span data-stu-id="e4948-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="e4948-120">たとえば、該当する機能が明示的に実装されていない限り、トークンを使用してユーザー アカウントを管理したり、ユーザーのパスワードを変更したりできません。</span><span class="sxs-lookup"><span data-stu-id="e4948-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="e4948-121">トークンの有効期間は短く、既定では 1 時間です。これにより、攻撃時間が制限されます。</span><span class="sxs-lookup"><span data-stu-id="e4948-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="e4948-122">トークンは、いつでも取り消すことができます。</span><span class="sxs-lookup"><span data-stu-id="e4948-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="e4948-123">自己完結型の JWT は、クライアントとサーバーの認証プロセスを保証します。</span><span class="sxs-lookup"><span data-stu-id="e4948-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="e4948-124">たとえば、クライアントには、受信したトークンが正当であり、指定した認証プロセスの一環として出力されたことを検出して検証する手段があります。</span><span class="sxs-lookup"><span data-stu-id="e4948-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="e4948-125">サード パーティが認証プロセスの途中でトークンを切り替えようとすると、クライアントは切り替えられたトークンを検出して使用しないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="e4948-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="e4948-126">OAuth および OIDC を使用したトークンは、アプリが安全であることを確認するために、正しく動作しているユーザー エージェントに依存しません。</span><span class="sxs-lookup"><span data-stu-id="e4948-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="e4948-127">OAuth や OIDC などのトークンベースのプロトコルでは、同じセキュリティ特性のセットを使用して、ホストされたアプリとスタンドアロンのアプリの認証と認可を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e4948-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="e4948-128">OIDC を使用した認証プロセス</span><span class="sxs-lookup"><span data-stu-id="e4948-128">Authentication process with OIDC</span></span>

<span data-ttu-id="e4948-129">`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリには、OIDC を使用した認証と認可を実装するためのいくつかのプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="e4948-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="e4948-130">大まかにいうと、認証は次のようにして行われます。</span><span class="sxs-lookup"><span data-stu-id="e4948-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="e4948-131">匿名ユーザーがログイン ボタンを選択する、または [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性が適用されたページを要求すると、そのユーザーがアプリのログイン ページ (`/authentication/login`) にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="e4948-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="e4948-132">ログイン ページで、認証ライブラリが承認エンドポイントへのリダイレクトを準備します。</span><span class="sxs-lookup"><span data-stu-id="e4948-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="e4948-133">承認エンドポイントは、Blazor WebAssembly の外部にあり、別のオリジンでホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="e4948-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="e4948-134">エンドポイントは、ユーザーが認証されているかどうかを判断し、応答として 1 つ以上のトークンを発行する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="e4948-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="e4948-135">認証ライブラリは、認証応答を受信するためのログイン コールバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="e4948-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="e4948-136">ユーザーが認証されていない場合、そのユーザーは基になる認証システム (通常は ASP.NET Core の Identity) にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="e4948-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="e4948-137">ユーザーが既に認証されている場合、承認エンドポイントは適切なトークンを生成し、ブラウザーをログイン コールバック エンドポイント (`/authentication/login-callback`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="e4948-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="e4948-138">Blazor WebAssembly アプリでログイン コールバック エンドポイント (`/authentication/login-callback`) が読み込まれると、認証応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="e4948-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="e4948-139">認証プロセスが正常に完了した場合、ユーザーは認証され、必要に応じてユーザーが要求した元の保護された URL に戻されます。</span><span class="sxs-lookup"><span data-stu-id="e4948-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="e4948-140">何らかの理由で認証プロセスが失敗した場合、ユーザーはログイン失敗ページ (`/authentication/login-failed`) に送信され、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e4948-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>
  
## <a name="implementation-guidance"></a><span data-ttu-id="e4948-141">実装ガイダンス</span><span class="sxs-lookup"><span data-stu-id="e4948-141">Implementation guidance</span></span>

<span data-ttu-id="e4948-142">この*概要*の記事では、特定のプロバイダーに対して Blazor WebAssembly アプリのユーザーを認証する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e4948-142">Articles under this *Overview* provide information on authenticating users in Blazor WebAssembly apps against specific providers.</span></span>

<span data-ttu-id="e4948-143">スタンドアロンの Blazor WebAssembly アプリ:</span><span class="sxs-lookup"><span data-stu-id="e4948-143">Standalone Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="e4948-144">OIDC プロバイダーと WebAssembly 認証ライブラリ向けの一般的なガイダンス</span><span class="sxs-lookup"><span data-stu-id="e4948-144">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:security/blazor/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="e4948-145">Microsoft アカウント</span><span class="sxs-lookup"><span data-stu-id="e4948-145">Microsoft Accounts</span></span>](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="e4948-146">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="e4948-146">Azure Active Directory (AAD)</span></span>](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="e4948-147">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="e4948-147">Azure Active Directory (AAD) B2C</span></span>](xref:security/blazor/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="e4948-148">ホストされた Blazor WebAssembly アプリ:</span><span class="sxs-lookup"><span data-stu-id="e4948-148">Hosted Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="e4948-149">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="e4948-149">Azure Active Directory (AAD)</span></span>](xref:security/blazor/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="e4948-150">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="e4948-150">Azure Active Directory (AAD) B2C</span></span>](xref:security/blazor/webassembly/hosted-with-azure-active-directory-b2c)
* <span data-ttu-id="e4948-151">[Identity サーバー](xref:security/blazor/webassembly/hosted-with-identity-server)</span><span class="sxs-lookup"><span data-stu-id="e4948-151">[Identity Server](xref:security/blazor/webassembly/hosted-with-identity-server)</span></span>

<span data-ttu-id="e4948-152">構成の詳細なガイダンスについては、<xref:security/blazor/webassembly/additional-scenarios> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e4948-152">For further guidance on configuration, see <xref:security/blazor/webassembly/additional-scenarios>.</span></span>
