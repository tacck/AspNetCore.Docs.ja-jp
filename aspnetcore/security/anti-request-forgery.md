---
title: ASP.NET Core でのクロスサイト要求偽造 (XSRF/CSRF) 攻撃を防ぐ
author: steve-smith
description: 悪意のある web サイトがクライアントブラウザーとアプリの間の対話に影響を与える可能性がある web アプリに対する攻撃を防ぐ方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/anti-request-forgery
ms.openlocfilehash: 4e7e7a89daaee533f648efdb2c621399225f57be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774005"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="24f61-103">ASP.NET Core でのクロスサイト要求偽造 (XSRF/CSRF) 攻撃を防ぐ</span><span class="sxs-lookup"><span data-stu-id="24f61-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="24f61-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)、[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="24f61-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="24f61-105">クロスサイト要求偽造 (XSRF または CSRF とも呼ばれます) は、web ホストアプリに対する攻撃であり、悪意のある web アプリがクライアントブラウザーとそのブラウザーを信頼する web アプリとの間の対話に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="24f61-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="24f61-106">これらの攻撃は、web ブラウザーがすべての要求を web サイトに自動的に送信するために発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="24f61-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="24f61-107">この形式の悪用は、*ワンクリック攻撃*または*セッション*によっても知られています。これは、攻撃がユーザーの以前に認証されたセッションを利用しているためです。</span><span class="sxs-lookup"><span data-stu-id="24f61-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="24f61-108">CSRF 攻撃の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="24f61-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="24f61-109">ユーザーは、フォーム`www.good-banking-site.com`認証を使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="24f61-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="24f61-110">サーバーは、ユーザーを認証し、認証 cookie を含む応答を発行します。</span><span class="sxs-lookup"><span data-stu-id="24f61-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="24f61-111">サイトは、有効な認証クッキーを使用して受信したすべての要求を信頼しているため、攻撃に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="24f61-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="24f61-112">ユーザーが悪意のあるサイト ( `www.bad-crook-site.com`) にアクセスしています。</span><span class="sxs-lookup"><span data-stu-id="24f61-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="24f61-113">悪意のあるサイト`www.bad-crook-site.com`には、次のような HTML フォームが含まれています。</span><span class="sxs-lookup"><span data-stu-id="24f61-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="24f61-114">悪意のあるサイトで`action`はなく、脆弱なサイトにフォームが投稿されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="24f61-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="24f61-115">これは CSRF の "クロスサイト" 部分です。</span><span class="sxs-lookup"><span data-stu-id="24f61-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="24f61-116">ユーザーは [送信] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="24f61-116">The user selects the submit button.</span></span> <span data-ttu-id="24f61-117">ブラウザーは要求を行い、要求されたドメインの認証 cookie を`www.good-banking-site.com`自動的に含めます。</span><span class="sxs-lookup"><span data-stu-id="24f61-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="24f61-118">要求は、ユーザーの`www.good-banking-site.com`認証コンテキストを使用してサーバー上で実行され、認証されたユーザーが実行を許可されている任意のアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="24f61-119">フォームを送信するためにユーザーがボタンを選択するシナリオに加えて、悪意のあるサイトも考えられます。</span><span class="sxs-lookup"><span data-stu-id="24f61-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="24f61-120">フォームを自動的に送信するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="24f61-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="24f61-121">フォーム送信を AJAX 要求として送信します。</span><span class="sxs-lookup"><span data-stu-id="24f61-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="24f61-122">CSS を使用してフォームを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="24f61-122">Hide the form using CSS.</span></span>

<span data-ttu-id="24f61-123">これらの代替シナリオでは、最初に悪意のあるサイトにアクセスしていないユーザーからの操作や入力は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="24f61-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="24f61-124">HTTPS を使用しても CSRF 攻撃を防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="24f61-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="24f61-125">悪意のあるサイトは、 `https://www.good-banking-site.com/`セキュリティで保護されていない要求を送信するのと同じように簡単に要求を送信できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="24f61-126">一部の攻撃では、GET 要求に応答するエンドポイントを対象にしています。この場合、イメージタグを使用してアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="24f61-127">この形式の攻撃は、イメージを許可し、JavaScript をブロックするフォーラムサイトで共通です。</span><span class="sxs-lookup"><span data-stu-id="24f61-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="24f61-128">GET 要求の状態を変更するアプリ (変数またはリソースが変更された場合) は、悪意のある攻撃に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="24f61-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="24f61-129">**状態を変更する GET 要求は安全ではありません。GET 要求の状態を変更しないことをお勧めします。**</span><span class="sxs-lookup"><span data-stu-id="24f61-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="24f61-130">次の理由により、認証に cookie を使用する web アプリに対して CSRF 攻撃を実行できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="24f61-131">ブラウザーは、web アプリによって発行された cookie を格納します。</span><span class="sxs-lookup"><span data-stu-id="24f61-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="24f61-132">保存される cookie には、認証されたユーザーのセッション cookie が含まれます。</span><span class="sxs-lookup"><span data-stu-id="24f61-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="24f61-133">ブラウザーは、アプリへの要求がブラウザー内でどのように生成されたかに関係なく、ドメインに関連付けられているすべての cookie を要求ごとに web アプリに送信します。</span><span class="sxs-lookup"><span data-stu-id="24f61-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="24f61-134">ただし、CSRF 攻撃は cookie を利用することに限定されません。</span><span class="sxs-lookup"><span data-stu-id="24f61-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="24f61-135">たとえば、基本認証とダイジェスト認証も脆弱です。</span><span class="sxs-lookup"><span data-stu-id="24f61-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="24f61-136">ユーザーが基本認証またはダイジェスト認証を使用してサインインすると、セッション&dagger;が終了するまで、ブラウザーは資格情報を自動的に送信します。</span><span class="sxs-lookup"><span data-stu-id="24f61-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="24f61-137">&dagger;このコンテキストでは、*セッション*とは、ユーザーが認証されるクライアント側のセッションを指します。</span><span class="sxs-lookup"><span data-stu-id="24f61-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="24f61-138">サーバー側セッションや[ASP.NET Core セッションミドルウェア](xref:fundamentals/app-state)とは無関係です。</span><span class="sxs-lookup"><span data-stu-id="24f61-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="24f61-139">ユーザーは、次のように予防措置を講じて CSRF 脆弱性を防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="24f61-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="24f61-140">Web apps の使用が完了したら、サインオフします。</span><span class="sxs-lookup"><span data-stu-id="24f61-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="24f61-141">ブラウザーの cookie を定期的にクリアします。</span><span class="sxs-lookup"><span data-stu-id="24f61-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="24f61-142">ただし、CSRF 脆弱性は、エンドユーザーではなく、根本的に web アプリに問題があります。</span><span class="sxs-lookup"><span data-stu-id="24f61-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="24f61-143">認証の基礎</span><span class="sxs-lookup"><span data-stu-id="24f61-143">Authentication fundamentals</span></span>

<span data-ttu-id="24f61-144">Cookie ベースの認証は、一般的な認証形式です。</span><span class="sxs-lookup"><span data-stu-id="24f61-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="24f61-145">特にシングルページアプリケーション (spa) では、トークンベースの認証システムの普及が高まっています。</span><span class="sxs-lookup"><span data-stu-id="24f61-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="24f61-146">Cookie ベースの認証</span><span class="sxs-lookup"><span data-stu-id="24f61-146">Cookie-based authentication</span></span>

<span data-ttu-id="24f61-147">ユーザー名とパスワードを使用して認証を行うと、認証と承認に使用できる認証チケットを含むトークンが発行されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="24f61-148">トークンは、クライアントが行うすべての要求に付随する cookie として格納されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="24f61-149">この cookie の生成と検証は、Cookie 認証ミドルウェアによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="24f61-150">[ミドルウェア](xref:fundamentals/middleware/index)は、ユーザープリンシパルを暗号化された cookie にシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="24f61-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="24f61-151">後続の要求では、ミドルウェアが cookie を検証し、プリンシパルを再作成し、そのプリンシパルを[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)の[User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="24f61-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="24f61-152">トークンベースの認証</span><span class="sxs-lookup"><span data-stu-id="24f61-152">Token-based authentication</span></span>

<span data-ttu-id="24f61-153">ユーザーが認証されると、トークンが発行されます (偽造防止トークンではありません)。</span><span class="sxs-lookup"><span data-stu-id="24f61-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="24f61-154">このトークンには、[要求](/dotnet/framework/security/claims-based-identity-model)の形式でユーザー情報が含まれているか、アプリがアプリで保持されているユーザー状態を示す参照トークンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="24f61-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="24f61-155">ユーザーが認証を必要とするリソースにアクセスしようとすると、トークンは、ベアラートークンの形式で追加の authorization ヘッダーを使用してアプリに送信されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="24f61-156">これにより、アプリはステートレスになります。</span><span class="sxs-lookup"><span data-stu-id="24f61-156">This makes the app stateless.</span></span> <span data-ttu-id="24f61-157">後続の各要求では、サーバー側の検証要求でトークンが渡されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="24f61-158">このトークンは*暗号化さ*れていません。これは*エンコード*されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="24f61-159">サーバーでは、トークンをデコードして情報にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="24f61-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="24f61-160">後続の要求でトークンを送信するには、ブラウザーのローカルストレージにトークンを格納します。</span><span class="sxs-lookup"><span data-stu-id="24f61-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="24f61-161">トークンがブラウザーのローカルストレージに格納されている場合、CSRF の脆弱性について心配しないでください。</span><span class="sxs-lookup"><span data-stu-id="24f61-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="24f61-162">CSRF は、トークンが cookie に格納されている場合に問題になります。</span><span class="sxs-lookup"><span data-stu-id="24f61-162">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="24f61-163">詳細については、GitHub の issue [SPA のコードサンプル](https://github.com/dotnet/AspNetCore.Docs/issues/13369)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="24f61-163">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="24f61-164">1つのドメインでホストされている複数のアプリ</span><span class="sxs-lookup"><span data-stu-id="24f61-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="24f61-165">共有ホスティング環境は、セッションハイジャック、ログイン CSRF、およびその他の攻撃に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="24f61-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="24f61-166">とは異なるホストですが、 `*.contoso.net`ドメイン内のホスト間には暗黙的な信頼関係があります。 `example2.contoso.net` `example1.contoso.net`</span><span class="sxs-lookup"><span data-stu-id="24f61-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="24f61-167">この暗黙的な信頼関係により、信頼できない可能性のあるホストが互いの cookie に影響を与えることがあります (AJAX 要求を管理する同じオリジンポリシーは、必ずしも HTTP クッキーに適用されるわけではありません)。</span><span class="sxs-lookup"><span data-stu-id="24f61-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="24f61-168">同じドメインでホストされているアプリ間で信頼された cookie を利用する攻撃は、ドメインを共有しないことで回避できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-168">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="24f61-169">各アプリが独自のドメインでホストされている場合、悪用に対する暗黙のクッキーの信頼関係はありません。</span><span class="sxs-lookup"><span data-stu-id="24f61-169">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="24f61-170">ASP.NET Core 偽造防止の構成</span><span class="sxs-lookup"><span data-stu-id="24f61-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="24f61-171">ASP.NET Core は[ASP.NET Core データ保護](xref:security/data-protection/introduction)を使用してアンチ偽造を実装します。</span><span class="sxs-lookup"><span data-stu-id="24f61-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="24f61-172">データ保護スタックは、サーバーファームで動作するように構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="24f61-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="24f61-173">詳細については、「[データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="24f61-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24f61-174">次のいずれかの Api がで`Startup.ConfigureServices`呼び出されると、アンチ偽造ミドルウェアが[依存関係の注入](xref:fundamentals/dependency-injection)コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="24f61-175">が呼び出されると<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 、アンチ偽造ミドルウェアが[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーに追加されます。`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="24f61-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="24f61-176">ASP.NET Core 2.0 以降では、 [Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper)は、アンチ偽造トークンを HTML フォーム要素に挿入します。</span><span class="sxs-lookup"><span data-stu-id="24f61-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="24f61-177">Razor ファイル内の次のマークアップは、アンチ偽造トークンを自動的に生成します。</span><span class="sxs-lookup"><span data-stu-id="24f61-177">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="24f61-178">同様に、フォームのメソッドが GET でない場合、 [Ihtmlhelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform)は既定で偽造防止トークンを生成します。</span><span class="sxs-lookup"><span data-stu-id="24f61-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="24f61-179">HTML フォーム要素のアンチ偽造トークンの自動生成は、 `<form>`タグに`method="post"`属性が含まれており、次のいずれかが当てはまる場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="24f61-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="24f61-180">Action 属性が空です (`action=""`)。</span><span class="sxs-lookup"><span data-stu-id="24f61-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="24f61-181">Action 属性が指定され`<form method="post">`ていません ()。</span><span class="sxs-lookup"><span data-stu-id="24f61-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="24f61-182">HTML フォーム要素のアンチ偽造トークンの自動生成は無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="24f61-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="24f61-183">属性を使用してアンチ偽造`asp-antiforgery`トークンを明示的に無効にします。</span><span class="sxs-lookup"><span data-stu-id="24f61-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="24f61-184">Form 要素は、タグヘルパー [! オプトアウトシンボル](xref:mvc/views/tag-helpers/intro#opt-out)を使用してタグヘルパーをオプトアウトします。</span><span class="sxs-lookup"><span data-stu-id="24f61-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="24f61-185">ビューから`FormTagHelper`を削除します。</span><span class="sxs-lookup"><span data-stu-id="24f61-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="24f61-186">`FormTagHelper`をビューから削除するには、次のディレクティブを Razor ビューに追加します。</span><span class="sxs-lookup"><span data-stu-id="24f61-186">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="24f61-187">[Razor Pages](xref:razor-pages/index)は XSRF/csrf から自動的に保護されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-187">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="24f61-188">詳細については、「 [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="24f61-188">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="24f61-189">CSRF 攻撃を防ぐための最も一般的な方法は、*シンクロナイザートークンパターン*(STP) を使用することです。</span><span class="sxs-lookup"><span data-stu-id="24f61-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="24f61-190">STP は、ユーザーがフォームデータを使用してページを要求したときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="24f61-191">サーバーは、現在のユーザーの id に関連付けられているトークンをクライアントに送信します。</span><span class="sxs-lookup"><span data-stu-id="24f61-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="24f61-192">クライアントは、トークンを検証のためにサーバーに送り返します。</span><span class="sxs-lookup"><span data-stu-id="24f61-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="24f61-193">サーバーが、認証されたユーザーの id と一致しないトークンを受け取った場合、要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="24f61-194">トークンは一意で、予測できません。</span><span class="sxs-lookup"><span data-stu-id="24f61-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="24f61-195">トークンを使用して、一連の要求を適切にシーケンス処理することもできます (たとえば、ページ 1 &ndash;ページ 2 &ndash;ページ 3)。</span><span class="sxs-lookup"><span data-stu-id="24f61-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="24f61-196">ASP.NET Core MVC および Razor Pages テンプレート内のすべてのフォームで、偽造防止トークンが生成されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-196">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="24f61-197">次の2つのビュー例では、偽造偽造トークンが生成されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="24f61-198">タグヘルパーと HTML ヘルパー `<form>` [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken)を使用せずに、次のように、アンチ偽造トークンを要素に明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="24f61-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="24f61-199">これらの各ケースでは、次のような非表示のフォームフィールドが ASP.NET Core によって追加されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="24f61-200">ASP.NET Core には、偽造防止トークンを操作するための3つの[フィルター](xref:mvc/controllers/filters)が含まれています。</span><span class="sxs-lookup"><span data-stu-id="24f61-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="24f61-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="24f61-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="24f61-202">Autovalidateアンチ Forgerytoken</span><span class="sxs-lookup"><span data-stu-id="24f61-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="24f61-203">Ignoreアンチ Forgerytoken</span><span class="sxs-lookup"><span data-stu-id="24f61-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="24f61-204">偽造防止オプション</span><span class="sxs-lookup"><span data-stu-id="24f61-204">Antiforgery options</span></span>

<span data-ttu-id="24f61-205">次の方法で偽造`Startup.ConfigureServices`防止[オプション](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions)をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="24f61-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="24f61-206">&dagger;[Cookiebuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder)クラスの`Cookie`プロパティを使用して、アンチ偽造プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="24f61-206">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="24f61-207">オプション</span><span class="sxs-lookup"><span data-stu-id="24f61-207">Option</span></span> | <span data-ttu-id="24f61-208">説明</span><span class="sxs-lookup"><span data-stu-id="24f61-208">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="24f61-209">クッキー</span><span class="sxs-lookup"><span data-stu-id="24f61-209">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="24f61-210">アンチ偽造 cookie の作成に使用する設定を決定します。</span><span class="sxs-lookup"><span data-stu-id="24f61-210">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="24f61-211">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="24f61-211">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="24f61-212">アンチ偽造システムがビューで偽造防止トークンをレンダリングするために使用する非表示フォームフィールドの名前。</span><span class="sxs-lookup"><span data-stu-id="24f61-212">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="24f61-213">HeaderName</span><span class="sxs-lookup"><span data-stu-id="24f61-213">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="24f61-214">アンチ偽造システムによって使用されるヘッダーの名前。</span><span class="sxs-lookup"><span data-stu-id="24f61-214">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="24f61-215">の`null`場合、システムはフォームデータのみを考慮します。</span><span class="sxs-lookup"><span data-stu-id="24f61-215">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="24f61-216">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="24f61-216">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="24f61-217">`X-Frame-Options`ヘッダーの生成を抑制するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="24f61-217">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="24f61-218">既定では、ヘッダーは値 "SAMEORIGIN" を使用して生成されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-218">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="24f61-219">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="24f61-219">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="24f61-220">オプション</span><span class="sxs-lookup"><span data-stu-id="24f61-220">Option</span></span> | <span data-ttu-id="24f61-221">説明</span><span class="sxs-lookup"><span data-stu-id="24f61-221">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="24f61-222">クッキー</span><span class="sxs-lookup"><span data-stu-id="24f61-222">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="24f61-223">アンチ偽造 cookie の作成に使用する設定を決定します。</span><span class="sxs-lookup"><span data-stu-id="24f61-223">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="24f61-224">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="24f61-224">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="24f61-225">Cookie のドメイン。</span><span class="sxs-lookup"><span data-stu-id="24f61-225">The domain of the cookie.</span></span> <span data-ttu-id="24f61-226">既定値は `null` です。</span><span class="sxs-lookup"><span data-stu-id="24f61-226">Defaults to `null`.</span></span> <span data-ttu-id="24f61-227">このプロパティは互換性のために残されていますが、今後のバージョンでは削除される予定です。</span><span class="sxs-lookup"><span data-stu-id="24f61-227">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="24f61-228">別の方法として、"Cookie. ドメイン" をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="24f61-228">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="24f61-229">CookieName</span><span class="sxs-lookup"><span data-stu-id="24f61-229">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="24f61-230">クッキーの名前。</span><span class="sxs-lookup"><span data-stu-id="24f61-230">The name of the cookie.</span></span> <span data-ttu-id="24f61-231">設定されていない場合、システムは、 [Defaultcookieprefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) ("で始まる一意の名前を生成します。AspNetCore。 ")。</span><span class="sxs-lookup"><span data-stu-id="24f61-231">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="24f61-232">このプロパティは互換性のために残されていますが、今後のバージョンでは削除される予定です。</span><span class="sxs-lookup"><span data-stu-id="24f61-232">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="24f61-233">別の方法として、Cookie.Name をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="24f61-233">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="24f61-234">CookiePath</span><span class="sxs-lookup"><span data-stu-id="24f61-234">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="24f61-235">クッキーに設定されたパス。</span><span class="sxs-lookup"><span data-stu-id="24f61-235">The path set on the cookie.</span></span> <span data-ttu-id="24f61-236">このプロパティは互換性のために残されていますが、今後のバージョンでは削除される予定です。</span><span class="sxs-lookup"><span data-stu-id="24f61-236">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="24f61-237">別の方法として、Cookie. Path をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="24f61-237">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="24f61-238">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="24f61-238">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="24f61-239">アンチ偽造システムがビューで偽造防止トークンをレンダリングするために使用する非表示フォームフィールドの名前。</span><span class="sxs-lookup"><span data-stu-id="24f61-239">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="24f61-240">HeaderName</span><span class="sxs-lookup"><span data-stu-id="24f61-240">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="24f61-241">アンチ偽造システムによって使用されるヘッダーの名前。</span><span class="sxs-lookup"><span data-stu-id="24f61-241">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="24f61-242">の`null`場合、システムはフォームデータのみを考慮します。</span><span class="sxs-lookup"><span data-stu-id="24f61-242">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="24f61-243">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="24f61-243">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="24f61-244">偽造防止システムによって HTTPS が要求されるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="24f61-244">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="24f61-245">の`true`場合、HTTPS 以外の要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="24f61-245">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="24f61-246">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="24f61-246">Defaults to `false`.</span></span> <span data-ttu-id="24f61-247">このプロパティは互換性のために残されていますが、今後のバージョンでは削除される予定です。</span><span class="sxs-lookup"><span data-stu-id="24f61-247">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="24f61-248">別の方法として、Cookie の SecurePolicy を設定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="24f61-248">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="24f61-249">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="24f61-249">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="24f61-250">`X-Frame-Options`ヘッダーの生成を抑制するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="24f61-250">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="24f61-251">既定では、ヘッダーは値 "SAMEORIGIN" を使用して生成されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-251">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="24f61-252">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="24f61-252">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="24f61-253">詳細については、「 [Cookieauthenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="24f61-253">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="24f61-254">偽造防止機能の構成 (Iアンチ偽造)</span><span class="sxs-lookup"><span data-stu-id="24f61-254">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="24f61-255">[Iアンチ偽造](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)は、偽造防止機能を構成するための API を提供します。</span><span class="sxs-lookup"><span data-stu-id="24f61-255">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="24f61-256">`IAntiforgery`は、 `Configure` `Startup`クラスのメソッドで要求できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-256">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="24f61-257">次の例では、アプリのホームページからミドルウェアを使用して、アンチ偽造トークンを生成し、それをクッキーとして応答に送信します (このトピックで後述する既定の角度の名前付け規則を使用します)。</span><span class="sxs-lookup"><span data-stu-id="24f61-257">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="24f61-258">偽造防止の検証を必要とする</span><span class="sxs-lookup"><span data-stu-id="24f61-258">Require antiforgery validation</span></span>

<span data-ttu-id="24f61-259">[Validateアンチ Forgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)は、個々のアクション、コントローラー、またはグローバルに適用できるアクションフィルターです。</span><span class="sxs-lookup"><span data-stu-id="24f61-259">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="24f61-260">このフィルターが適用されているアクションに対して行われた要求は、要求に有効な偽造偽造トークンが含まれていない限り、ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="24f61-260">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="24f61-261">`ValidateAntiForgeryToken`属性には、HTTP GET 要求を含む、マークするアクションメソッドへの要求にトークンが必要です。</span><span class="sxs-lookup"><span data-stu-id="24f61-261">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="24f61-262">`ValidateAntiForgeryToken`属性がアプリのコントローラー全体に適用される場合は、 `IgnoreAntiforgeryToken`属性でオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="24f61-262">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="24f61-263">ASP.NET Core は、要求を自動的に取得するための偽造防止トークンの追加をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="24f61-263">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="24f61-264">安全でない HTTP メソッドに対してのみ偽造防止トークンを自動的に検証する</span><span class="sxs-lookup"><span data-stu-id="24f61-264">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="24f61-265">ASP.NET Core アプリは、安全な HTTP メソッド (GET、HEAD、OPTIONS、および TRACE) に対して偽造防止トークンを生成しません。</span><span class="sxs-lookup"><span data-stu-id="24f61-265">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="24f61-266">`ValidateAntiForgeryToken`属性を広範に適用して属性で`IgnoreAntiforgeryToken`オーバーライドするのではなく、 [Autovalidateアンチ forgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-266">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="24f61-267">この属性は、 `ValidateAntiForgeryToken`属性と同じように動作しますが、次の HTTP メソッドを使用して行われた要求に対してトークンを必要としない点が異なります。</span><span class="sxs-lookup"><span data-stu-id="24f61-267">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="24f61-268">GET</span><span class="sxs-lookup"><span data-stu-id="24f61-268">GET</span></span>
* <span data-ttu-id="24f61-269">HEAD</span><span class="sxs-lookup"><span data-stu-id="24f61-269">HEAD</span></span>
* <span data-ttu-id="24f61-270">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="24f61-270">OPTIONS</span></span>
* <span data-ttu-id="24f61-271">TRACE</span><span class="sxs-lookup"><span data-stu-id="24f61-271">TRACE</span></span>

<span data-ttu-id="24f61-272">非 API のシナリオ`AutoValidateAntiforgeryToken`では、広範なを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="24f61-272">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="24f61-273">これにより、POST アクションが既定で保護されるようになります。</span><span class="sxs-lookup"><span data-stu-id="24f61-273">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="24f61-274">代替手段は、が個々のアクションメソッドに適用さ`ValidateAntiForgeryToken`れない限り、既定で偽造防止トークンを無視することです。</span><span class="sxs-lookup"><span data-stu-id="24f61-274">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="24f61-275">このシナリオでは、POST アクションメソッドが誤って保護されないままになり、アプリが CSRF 攻撃に対して脆弱になる可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="24f61-275">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="24f61-276">すべての投稿は、アンチ偽造トークンを送信する必要があります。</span><span class="sxs-lookup"><span data-stu-id="24f61-276">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="24f61-277">Api には、トークンの cookie 以外の部分を送信するための自動メカニズムがありません。</span><span class="sxs-lookup"><span data-stu-id="24f61-277">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="24f61-278">実装は、クライアントコードの実装によって異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="24f61-278">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="24f61-279">いくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="24f61-279">Some examples are shown below:</span></span>

<span data-ttu-id="24f61-280">クラスレベルの例:</span><span class="sxs-lookup"><span data-stu-id="24f61-280">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="24f61-281">グローバルな例:</span><span class="sxs-lookup"><span data-stu-id="24f61-281">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="24f61-282">サーヴィス.AddMvc (オプション => オプション。Filters。 Add (新しい Autovalidateアンチ Forgerytokenattribute ());</span><span class="sxs-lookup"><span data-stu-id="24f61-282">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="24f61-283">グローバルまたはコントローラーのアンチ偽造属性を上書きする</span><span class="sxs-lookup"><span data-stu-id="24f61-283">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="24f61-284">[Ignoreアンチ Forgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)フィルターは、特定のアクション (またはコントローラー) に対して偽造防止トークンが不要になるようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-284">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="24f61-285">このフィルターを適用すると`ValidateAntiForgeryToken` 、 `AutoValidateAntiforgeryToken`より高いレベルで指定されたフィルター (グローバルまたはコントローラー上) が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="24f61-285">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="24f61-286">認証後のトークンの更新</span><span class="sxs-lookup"><span data-stu-id="24f61-286">Refresh tokens after authentication</span></span>

<span data-ttu-id="24f61-287">ユーザーを認証した後で、ユーザーをビューページまたはRazorページページにリダイレクトすることによって、トークンを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="24f61-287">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="24f61-288">JavaScript、AJAX、および SPAs</span><span class="sxs-lookup"><span data-stu-id="24f61-288">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="24f61-289">従来の HTML ベースのアプリでは、非表示のフォームフィールドを使用して、偽造防止トークンがサーバーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-289">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="24f61-290">最新の JavaScript ベースのアプリと SPAs では、多くの要求がプログラムによって行われます。</span><span class="sxs-lookup"><span data-stu-id="24f61-290">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="24f61-291">これらの AJAX 要求では、他の手法 (要求ヘッダーや cookie など) を使用してトークンを送信できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-291">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="24f61-292">Cookie を使用して認証トークンを格納し、サーバーで API 要求を認証する場合、CSRF は潜在的な問題です。</span><span class="sxs-lookup"><span data-stu-id="24f61-292">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="24f61-293">ローカルストレージを使用してトークンを格納している場合は、ローカルストレージからの値がすべての要求でサーバーに自動的に送信されないため、CSRF 脆弱性が軽減される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="24f61-293">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="24f61-294">したがって、ローカルストレージを使用して、クライアントにアンチ偽造トークンを格納し、要求ヘッダーとしてトークンを送信することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="24f61-294">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="24f61-295">JavaScript</span><span class="sxs-lookup"><span data-stu-id="24f61-295">JavaScript</span></span>

<span data-ttu-id="24f61-296">ビューで JavaScript を使用すると、ビュー内からサービスを使用してトークンを作成できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-296">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="24f61-297">ビューに[AspNetCore の偽造](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)サービスを挿入し、 [Getandstoretokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="24f61-297">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="24f61-298">この方法では、サーバーからの cookie の設定やクライアントからの読み取りを直接処理する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="24f61-298">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="24f61-299">前の例では、JavaScript を使用して、AJAX POST ヘッダーの隠しフィールド値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="24f61-299">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="24f61-300">JavaScript では、cookie 内のトークンにアクセスし、cookie の内容を使用して、トークンの値を持つヘッダーを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="24f61-300">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="24f61-301">スクリプトがという`X-CSRF-TOKEN`ヘッダー内のトークンを送信するように要求した場合、その`X-CSRF-TOKEN`ヘッダーを検索するようにアンチ偽造サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="24f61-301">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="24f61-302">次の例では、JavaScript を使用して、適切なヘッダーを含む AJAX 要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="24f61-302">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="24f61-303">AngularJS</span><span class="sxs-lookup"><span data-stu-id="24f61-303">AngularJS</span></span>

<span data-ttu-id="24f61-304">AngularJS は、規約を使用して CSRF に対処します。</span><span class="sxs-lookup"><span data-stu-id="24f61-304">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="24f61-305">サーバーがという名前`XSRF-TOKEN`のクッキーを送信した場合`$http` 、AngularJS サービスは、サーバーに要求を送信するときに、cookie の値をヘッダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="24f61-305">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="24f61-306">このプロセスは自動的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="24f61-306">This process is automatic.</span></span> <span data-ttu-id="24f61-307">ヘッダーは、クライアントで明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="24f61-307">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="24f61-308">ヘッダー名が`X-XSRF-TOKEN`です。</span><span class="sxs-lookup"><span data-stu-id="24f61-308">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="24f61-309">サーバーは、このヘッダーを検出し、その内容を検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="24f61-309">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="24f61-310">アプリケーションの起動時に、ASP.NET Core API がこの規則を使用するようにするには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="24f61-310">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="24f61-311">という`XSRF-TOKEN`クッキーにトークンを提供するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="24f61-311">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="24f61-312">という名前`X-XSRF-TOKEN`のヘッダーを検索するようにアンチ偽造サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="24f61-312">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="24f61-313">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="24f61-313">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="24f61-314">アンチ偽造の延長</span><span class="sxs-lookup"><span data-stu-id="24f61-314">Extend antiforgery</span></span>

<span data-ttu-id="24f61-315">[Iアンチ Forgeryadditionaldataprovider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider)型を使用すると、開発者は各トークンの追加データをラウンドトリップさせることで、csrf システムの動作を拡張できます。</span><span class="sxs-lookup"><span data-stu-id="24f61-315">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="24f61-316">[Getadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata)メソッドは、フィールドトークンが生成されるたびに呼び出され、戻り値は生成されたトークン内に埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="24f61-316">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="24f61-317">実装者は、タイムスタンプ、nonce、またはその他の値を返し、 [Validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata)を呼び出して、トークンが検証されたときにこのデータを検証することができます。</span><span class="sxs-lookup"><span data-stu-id="24f61-317">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="24f61-318">クライアントのユーザー名は、生成されたトークンに既に埋め込まれているため、この情報を含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="24f61-318">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="24f61-319">トークンに追加データ`IAntiForgeryAdditionalDataProvider`が含まれていても構成されていない場合、補足データは検証されません。</span><span class="sxs-lookup"><span data-stu-id="24f61-319">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24f61-320">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="24f61-320">Additional resources</span></span>

* <span data-ttu-id="24f61-321">[Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (owasp) での[csrf](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 。</span><span class="sxs-lookup"><span data-stu-id="24f61-321">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
