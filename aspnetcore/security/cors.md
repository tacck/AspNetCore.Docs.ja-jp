---
title: ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする
author: rick-anderson
description: ASP.NET Core アプリでのクロスオリジン要求を許可または拒否するための標準として CORS を使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: dc8e68ef482025443147eeb27bd3f245c1a1a5ed
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212890"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="e15a9-103">ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする</span><span class="sxs-lookup"><span data-stu-id="e15a9-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e15a9-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="e15a9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="e15a9-105">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="e15a9-106">ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="e15a9-107">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="e15a9-108">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="e15a9-109">場合によっては、他のサイトがアプリに対してクロスオリジン要求を行うことを許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="e15a9-110">詳細については、「 [MOZILLA CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="e15a9-111">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="e15a9-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="e15a9-112">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="e15a9-113">は、CORS 緩和され security のセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="e15a9-114">CORS を許可すると、API の安全性が向上しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="e15a9-115">詳細については、「 [CORS のしくみ](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="e15a9-116">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="e15a9-117">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="e15a9-118">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e15a9-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="e15a9-119">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="e15a9-119">Same origin</span></span>

<span data-ttu-id="e15a9-120">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="e15a9-121">この2つの Url のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="e15a9-122">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="e15a9-123">`https://example.net`: 異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="e15a9-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="e15a9-124">`https://www.example.com/foo.html`: 異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="e15a9-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="e15a9-125">`http://example.com/foo.html`: 異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="e15a9-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="e15a9-126">`https://example.com:9000/foo.html`: 別のポート</span><span class="sxs-lookup"><span data-stu-id="e15a9-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="e15a9-127">CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="e15a9-127">Enable CORS</span></span>

<span data-ttu-id="e15a9-128">CORS を有効にするには、次の3つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="e15a9-129">[名前付きポリシー](#np)または既定の[ポリシー](#dp)を使用するミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="e15a9-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="e15a9-130">[エンドポイントルーティング](#ecors)を使用する。</span><span class="sxs-lookup"><span data-stu-id="e15a9-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="e15a9-131">[[Enablecors]](#attr)属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="e15a9-132">名前付きポリシーで[[Enablecors]](#attr)属性を使用すると、CORS をサポートするエンドポイントを制限するための最も細かい制御が可能になります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="e15a9-133"><xref:Owin.CorsExtensions.UseCors%2A>を使用する場合は、前にを呼び出す必要があり <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> `UseResponseCaching` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="e15a9-134">各方法の詳細については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="e15a9-135">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="e15a9-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="e15a9-136">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="e15a9-137">次のコードは、指定されたオリジンを持つすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="e15a9-138">上記のコードでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-138">The preceding code:</span></span>

* <span data-ttu-id="e15a9-139">ポリシー名をに設定 `_myAllowSpecificOrigins` します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="e15a9-140">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="e15a9-141">拡張メソッドを呼び出し、 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> CORS ポリシーを指定し `_myAllowSpecificOrigins` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="e15a9-142">`UseCors`CORS ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="e15a9-143">への呼び出しは `UseCors` `UseRouting` 、の後、の前に配置する必要があり `UseAuthorization` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="e15a9-144">詳細については、「[ミドルウェアの順序](xref:fundamentals/middleware/index#middleware-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="e15a9-145"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="e15a9-146">ラムダはオブジェクトを受け取り <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="e15a9-147">などの[構成オプション](#cors-policy-options)に `WithOrigins` ついては、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="e15a9-148">`_myAllowSpecificOrigins`すべてのコントローラーエンドポイントに対して CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="e15a9-149">特定のエンドポイントに CORS ポリシーを適用するには、「[エンドポイントルーティング](#ecors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="e15a9-150">[応答キャッシュミドルウェア](xref:performance/caching/middleware)を使用する場合は、の <xref:Owin.CorsExtensions.UseCors%2A> 前にを呼び出し <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="e15a9-151">エンドポイントルーティングでは、CORS ミドルウェアをとの呼び出しの間で実行するように構成する**必要があり** `UseRouting` `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="e15a9-152">前のコードのようなコードをテストする方法については、「[テスト CORS](#testc) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="e15a9-153">メソッド呼び出しによって、 <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> アプリのサービスコンテナーに CORS サービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="e15a9-154">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="e15a9-155">メソッドは、 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 次のコードに示すように、連結できます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="e15a9-156">注: 指定された URL に末尾にスラッシュ () を含めることは**できません** `/` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="e15a9-157">URL がで終了した場合 `/` 、比較はを返し、 `false` ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="e15a9-158">既定のポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="e15a9-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="e15a9-159">次の強調表示されたコードは、既定の CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="e15a9-160">上記のコードは、すべてのコントローラーエンドポイントに既定の CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="e15a9-161">エンドポイント ルーティングで CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="e15a9-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="e15a9-162">現在、を使用して、エンドポイントごとに CORS を有効にする `RequireCors` ことは、[自動プレフライト要求](#apf)をサポートして**いません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="e15a9-163">詳細については、こちらの[GitHub の問題](https://github.com/dotnet/aspnetcore/issues/20709)を参照してください。また、[エンドポイントルーティングと [HttpOptions] を使用](#tcer)して CORS をテストしてください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="e15a9-164">エンドポイントルーティングを使用すると、一連の拡張メソッドを使用して、エンドポイントごとに CORS を有効にすることができ <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="e15a9-165">上記のコードにより、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-165">In the preceding code:</span></span>

* <span data-ttu-id="e15a9-166">`app.UseCors`CORS ミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="e15a9-167">既定のポリシーが構成されていないため、 `app.UseCors()` 単独で CORS を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="e15a9-168">`/echo`およびコントローラーエンドポイントは、指定されたポリシーを使用して、クロスオリジン要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="e15a9-169">`/echo2`既定のポリシーが指定されていないため、と Razor Pages エンドポイントはクロスオリジン要求を許可し**ません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-169">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="e15a9-170">[[Disablecors]](#dc)属性では、を使用してエンドポイントルーティングによって有効にされた CORS は無効になり**ません** `RequireCors` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="e15a9-171">前述のようなコードをテストする方法については、「[エンドポイントルーティングを使用した CORS のテスト」および「[HttpOptions]](#tcer) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="e15a9-172">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="e15a9-172">Enable CORS with attributes</span></span>

<span data-ttu-id="e15a9-173">[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性を使用して cors を有効にし、cors を必要とするエンドポイントのみに名前付きポリシーを適用することで、最も細かい制御が可能になります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="e15a9-174">[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用するための代替手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="e15a9-175">属性は、 `[EnableCors]` すべてのエンドポイントではなく、選択したエンドポイントに対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="e15a9-176">`[EnableCors]`既定のポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="e15a9-177">`[EnableCors("{Policy String}")]`名前付きポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="e15a9-178">`[EnableCors]`属性は次のものに適用できます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-178">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="e15a9-179">改`PageModel`</span><span class="sxs-lookup"><span data-stu-id="e15a9-179"> Page `PageModel`</span></span>
* <span data-ttu-id="e15a9-180">コントローラー</span><span class="sxs-lookup"><span data-stu-id="e15a9-180">Controller</span></span>
* <span data-ttu-id="e15a9-181">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="e15a9-181">Controller action method</span></span>

<span data-ttu-id="e15a9-182">属性を使用して、さまざまなポリシーをコントローラー、ページモデル、またはアクションメソッドに適用でき `[EnableCors]` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="e15a9-183">`[EnableCors]`属性がコントローラー、ページモデル、またはアクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、**両方**のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="e15a9-184">**ポリシーを組み合わせることはお勧めしません。** `[EnableCors]` **同じアプリ内ではなく、属性またはミドルウェアを使用します。**</span><span class="sxs-lookup"><span data-stu-id="e15a9-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="e15a9-185">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="e15a9-186">次のコードでは、2つの CORS ポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="e15a9-187">CORS 要求を制限する最も細かい制御:</span><span class="sxs-lookup"><span data-stu-id="e15a9-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="e15a9-188">`[EnableCors("MyPolicy")]`名前付きポリシーでを使用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="e15a9-189">既定のポリシーは定義しないでください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-189">Don't define a default policy.</span></span>
* <span data-ttu-id="e15a9-190">[エンドポイントルーティング](#ecors)を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="e15a9-191">次のセクションのコードは、上記のリストを満たしています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="e15a9-192">前のコードのようなコードをテストする方法については、「[テスト CORS](#testc) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="e15a9-193">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="e15a9-193">Disable CORS</span></span>

<span data-ttu-id="e15a9-194">[[Disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、[エンドポイントルーティング](#ecors)によって有効にされた CORS を無効にし**ません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="e15a9-195">次のコードでは、CORS ポリシーを定義してい `"MyPolicy"` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="e15a9-196">次のコードは、アクションの CORS を無効にし `GetValues2` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="e15a9-197">上記のコードでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-197">The preceding code:</span></span>

* <span data-ttu-id="e15a9-198">[エンドポイントルーティング](#ecors)で CORS を有効にしません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="e15a9-199">[既定の CORS ポリシー](#dp)は定義されていません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="e15a9-200">では、 [[Enablecors ("MyPolicy")]](#attr)を使用して `"MyPolicy"` 、コントローラーの CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="e15a9-201">メソッドの CORS を無効に `GetValues2` します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="e15a9-202">前のコードをテストする手順については、「[テスト CORS](#testc) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="e15a9-203">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="e15a9-203">CORS policy options</span></span>

<span data-ttu-id="e15a9-204">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="e15a9-205">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="e15a9-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="e15a9-206">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="e15a9-207">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="e15a9-208">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="e15a9-209">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="e15a9-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="e15a9-210">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="e15a9-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>はで呼び出され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e15a9-212">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="e15a9-213">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="e15a9-213">Set the allowed origins</span></span>

<span data-ttu-id="e15a9-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: 任意のスキーム (または) を持つすべてのオリジンからの CORS 要求を許可 `http` `https` します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="e15a9-215">`AllowAnyOrigin`*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e15a9-216">とを指定すると、 `AllowAnyOrigin` `AllowCredentials` 安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e15a9-217">アプリが両方の方法で構成されている場合、CORS サービスは無効な CORS 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="e15a9-218">`AllowAnyOrigin`プレフライト要求とヘッダーに影響し `Access-Control-Allow-Origin` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="e15a9-219">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e15a9-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインとオリジンが一致するようにするための関数として、ポリシーのプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="e15a9-221">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="e15a9-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="e15a9-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="e15a9-223">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="e15a9-224">プレフライト要求とヘッダーに影響し `Access-Control-Allow-Methods` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="e15a9-225">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="e15a9-226">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-226">Set the allowed request headers</span></span>

<span data-ttu-id="e15a9-227">CORS 要求で特定のヘッダーを送信できるようにするには、 [author 要求ヘッダー](https://xhr.spec.whatwg.org/#request)と呼ばれるを呼び出し、 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="e15a9-228">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="e15a9-229">`AllowAnyHeader`プレフライト要求および[アクセス制御-要求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)ヘッダーヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="e15a9-230">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e15a9-231">によって指定された特定のヘッダーに対して CORS ミドルウェアポリシーが一致するのは、ヘッダーがで記述されているヘッダーと `WithHeaders` 完全に一致する場合のみです `Access-Control-Request-Headers` `WithHeaders` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="e15a9-232">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="e15a9-233">CORS ミドルウェアは、次の要求ヘッダーを使用してプレフライト要求を拒否し `Content-Language` ます。 ([headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) は、には記載されていないため `WithHeaders` です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="e15a9-234">アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e15a9-235">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="e15a9-236">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-236">Set the exposed response headers</span></span>

<span data-ttu-id="e15a9-237">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="e15a9-238">詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="e15a9-239">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="e15a9-240">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-240">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="e15a9-241">アプリで他のヘッダーを使用できるようにするには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="e15a9-242">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="e15a9-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="e15a9-243">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="e15a9-244">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="e15a9-245">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-245">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="e15a9-246">クロスオリジン要求で資格情報を送信するには、クライアントがに設定されている必要があり `XMLHttpRequest.withCredentials` `true` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="e15a9-247">直接の使用 `XMLHttpRequest` :</span><span class="sxs-lookup"><span data-stu-id="e15a9-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="e15a9-248">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="e15a9-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="e15a9-249">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="e15a9-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="e15a9-250">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-250">The server must allow the credentials.</span></span> <span data-ttu-id="e15a9-251">クロスオリジンの資格情報を許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="e15a9-252">HTTP 応答には、 `Access-Control-Allow-Credentials` サーバーがクロスオリジン要求に対して資格情報を許可することをブラウザーに示すヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="e15a9-253">ブラウザーが資格情報を送信しても、応答に有効なヘッダーが含まれていない場合 `Access-Control-Allow-Credentials` 、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="e15a9-254">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="e15a9-255">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="e15a9-256">また、CORS 仕様では、ヘッダーが存在する場合、[オリジン] を [(すべてのオリジン)] に設定することもでき `"*"` `Access-Control-Allow-Credentials` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="e15a9-257">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="e15a9-257">Preflight requests</span></span>

<span data-ttu-id="e15a9-258">一部の CORS 要求については、ブラウザーは、実際の要求を行う前に、追加の[オプション](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="e15a9-259">この要求は[プレフライト要求](https://developer.mozilla.org/docs/Glossary/Preflight_request)と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="e15a9-260">次の**すべて**の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="e15a9-261">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="e15a9-262">アプリで `Accept` は、、、 `Accept-Language` `Content-Language` 、 `Content-Type` 、または `Last-Event-ID` 以外の要求ヘッダーは設定されません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="e15a9-263">ヘッダーには、設定されて `Content-Type` いる場合、次のいずれかの値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="e15a9-264">クライアント要求に対して設定された要求ヘッダーに適用される規則は、オブジェクトに対してを呼び出すことによって、アプリが設定するヘッダーに適用され `setRequestHeader` `XMLHttpRequest` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="e15a9-265">CORS 仕様は、これらのヘッダー[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="e15a9-266">このルールは、ブラウザーが設定できるヘッダー (、、など) には適用されません `User-Agent` `Host` `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="e15a9-267">次に示すのは、このドキュメントの「[テスト CORS](#testc) 」セクションの **[Put test]** ボタンから行ったプレフライト要求に似た応答の例です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="e15a9-268">プレフライト要求では、 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="e15a9-269">次のヘッダーが含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-269">It may include the following headers:</span></span>

* <span data-ttu-id="e15a9-270">[アクセス制御要求メソッド](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="e15a9-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="e15a9-271">[アクセス制御要求ヘッダー](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): アプリが実際の要求に対して設定する要求ヘッダーのリスト。</span><span class="sxs-lookup"><span data-stu-id="e15a9-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="e15a9-272">前述のように、これにはブラウザーが設定するヘッダー (など) は含まれません `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="e15a9-273">アクセス制御-許可-メソッド</span><span class="sxs-lookup"><span data-stu-id="e15a9-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="e15a9-274">プレフライト要求が拒否された場合、アプリは応答を返し `200 OK` ますが、CORS ヘッダーは設定しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="e15a9-275">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="e15a9-276">拒否されたプレフライト要求の例については、このドキュメントの「[テスト CORS](#testc) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="e15a9-277">F12 ツールを使用すると、コンソールアプリには、ブラウザーに応じて次のいずれかのようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="e15a9-278">Firefox: クロスオリジン要求がブロックされています: 同じオリジンポリシーで、でのリモートリソースの読み取りが許可されて `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` いません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="e15a9-279">(理由: CORS 要求が失敗しました)。</span><span class="sxs-lookup"><span data-stu-id="e15a9-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="e15a9-280">詳細情報</span><span class="sxs-lookup"><span data-stu-id="e15a9-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="e15a9-281">Chromium ベース: オリジン ' ' からの ' ' でのフェッチへのアクセス https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net が CORS ポリシーによってブロックされました。プレフライト要求への応答がアクセス制御チェックに合格しません: 要求されたリソースに ' アクセス制御-許可-オリジン ' ヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="e15a9-282">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="e15a9-283">特定のヘッダーを許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="e15a9-284">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="e15a9-285">ブラウザーの設定方法が一貫していません `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="e15a9-286">次のいずれかの場合:</span><span class="sxs-lookup"><span data-stu-id="e15a9-286">If either:</span></span>

* <span data-ttu-id="e15a9-287">ヘッダーは、`"*"`</span><span class="sxs-lookup"><span data-stu-id="e15a9-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="e15a9-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>が呼び出されます: 少なくとも、、、 `Accept` `Content-Type` `Origin` 、およびサポートするカスタムヘッダーを含めます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="e15a9-289">自動プレフライト要求コード</span><span class="sxs-lookup"><span data-stu-id="e15a9-289">Automatic preflight request code</span></span>

<span data-ttu-id="e15a9-290">CORS ポリシーを適用する場合は、次のいずれかの方法を実行します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="e15a9-291">でを呼び出すことにより、グローバルに `app.UseCors` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="e15a9-292">属性を使用し `[EnableCors]` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="e15a9-293">ASP.NET Core は、プレフライトオプション要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="e15a9-294">現在、を使用して、エンドポイントごとに CORS を有効にする `RequireCors` ことは、自動プレフライト要求をサポートして**いません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="e15a9-295">このドキュメントの「[テスト CORS](#testc) 」セクションでは、この動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="e15a9-296">プレフライト要求の [HttpOptions] 属性</span><span class="sxs-lookup"><span data-stu-id="e15a9-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="e15a9-297">CORS が適切なポリシーで有効になっている場合、ASP.NET Core は通常、CORS プレフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="e15a9-298">シナリオによっては、これが当てはまりません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="e15a9-299">たとえば、CORS を[エンドポイントルーティングと共](#ecors)に使用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="e15a9-300">次のコードでは、 [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)属性を使用して、OPTIONS 要求のエンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="e15a9-301">前のコードをテストする手順については、「[エンドポイントルーティングを使用した CORS のテスト」および「[HttpOptions]](#tcer) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="e15a9-302">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-302">Set the preflight expiration time</span></span>

<span data-ttu-id="e15a9-303">ヘッダーは、 `Access-Control-Max-Age` プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="e15a9-304">このヘッダーを設定するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="e15a9-305">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="e15a9-305">How CORS works</span></span>

<span data-ttu-id="e15a9-306">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="e15a9-307">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="e15a9-308">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="e15a9-309">たとえば、悪意のあるアクターがサイトに対して[クロスサイトスクリプティング (XSS)](xref:security/cross-site-scripting)を使用して、CORS が有効になっているサイトに対してクロスサイト要求を実行し、情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="e15a9-310">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="e15a9-311">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="e15a9-312">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="e15a9-313">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="e15a9-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="e15a9-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="e15a9-315">Postman</span><span class="sxs-lookup"><span data-stu-id="e15a9-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="e15a9-316">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="e15a9-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="e15a9-317">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="e15a9-318">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="e15a9-319">CORS のないブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="e15a9-320">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="e15a9-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="e15a9-321">JSONP は XHR を使用しないので、タグを使用して `<script>` 応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="e15a9-322">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="e15a9-323">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="e15a9-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="e15a9-324">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="e15a9-325">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="e15a9-326">配置された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の [[テストの配置] ボタン](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="e15a9-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="e15a9-327">次に、[[値](https://cors3.azurewebsites.net/)のテスト] ボタンからへのクロスオリジン要求の例を示し `https://cors1.azurewebsites.net/api/values` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="e15a9-328">`Origin`ヘッダー:</span><span class="sxs-lookup"><span data-stu-id="e15a9-328">The `Origin` header:</span></span>

* <span data-ttu-id="e15a9-329">要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="e15a9-330">は必須であり、ホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="e15a9-331">**一般的なヘッダー**</span><span class="sxs-lookup"><span data-stu-id="e15a9-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="e15a9-332">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="e15a9-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="e15a9-333">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="e15a9-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="e15a9-334">要求では、 `OPTIONS` サーバーは応答**Response headers**のヘッダーヘッダーを設定し `Access-Control-Allow-Origin: {allowed origin}` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="e15a9-335">たとえば、デプロイされた[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[Delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) button 要求には、 `OPTIONS` 次のヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="e15a9-336">**一般的なヘッダー**</span><span class="sxs-lookup"><span data-stu-id="e15a9-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="e15a9-337">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="e15a9-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="e15a9-338">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="e15a9-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="e15a9-339">上記の**応答ヘッダー**では、サーバーは応答で[アクセス制御-許可](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-339">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="e15a9-340">`https://cors1.azurewebsites.net`このヘッダーの値は、要求のヘッダーと一致し `Origin` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="e15a9-341"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>が呼び出されると、 `Access-Control-Allow-Origin: *` ワイルドカード値が返されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="e15a9-342">`AllowAnyOrigin`任意の発信元を許可します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="e15a9-343">応答にヘッダーが含まれていない場合 `Access-Control-Allow-Origin` 、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="e15a9-344">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="e15a9-345">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="e15a9-346">表示オプションの要求</span><span class="sxs-lookup"><span data-stu-id="e15a9-346">Display OPTIONS requests</span></span>

<span data-ttu-id="e15a9-347">既定では、Chrome および Edge ブラウザーでは、F12 ツールの [ネットワーク] タブの [要求] オプションが表示されません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="e15a9-348">これらのブラウザーでオプションの要求を表示するには:</span><span class="sxs-lookup"><span data-stu-id="e15a9-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="e15a9-349">`chrome://flags/#out-of-blink-cors` または `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="e15a9-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="e15a9-350">フラグを無効にします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-350">disable the flag.</span></span>
* <span data-ttu-id="e15a9-351">[再起動] をタップします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-351">restart.</span></span>

<span data-ttu-id="e15a9-352">既定では、Firefox によってオプションの要求が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="e15a9-353">IIS での CORS</span><span class="sxs-lookup"><span data-stu-id="e15a9-353">CORS in IIS</span></span>

<span data-ttu-id="e15a9-354">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合、CORS は Windows 認証の前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="e15a9-355">このシナリオをサポートするには、アプリ用に[IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールして構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="e15a9-356">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="e15a9-356">Test CORS</span></span>

<span data-ttu-id="e15a9-357">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)には、CORS をテストするコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="e15a9-358">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="e15a9-359">このサンプルは、ページが追加された API プロジェクトです Razor 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-359">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="e15a9-360">`WithOrigins("https://localhost:<port>");`サンプルアプリのテストにのみ使用してください[サンプルコードをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="e15a9-361">`ValuesController`テスト用のエンドポイントは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="e15a9-362">[Mydisplayrouteinfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)は、Rick.Doc. [routeinfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet パッケージによって提供され、ルート情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="e15a9-363">次のいずれかの方法を使用して、上記のサンプルコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="e15a9-364">でデプロイされたサンプルアプリを使用し [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="e15a9-365">サンプルをダウンロードする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="e15a9-366">`dotnet run`の既定の URL を使用して、サンプルを実行し `https://localhost:5001` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="e15a9-367">URL に対してポートを44398に設定して、Visual Studio からサンプルを実行し `https://localhost:44398` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="e15a9-368">F12 ツールでブラウザーを使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="e15a9-369">[**値**] をクリックし、[**ネットワーク**] タブでヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="e15a9-370">[**テストの配置**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-370">Select the **PUT test** button.</span></span> <span data-ttu-id="e15a9-371">OPTIONS 要求を表示する方法については、「[表示オプションの要求](#options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="e15a9-372">**Put テスト**では、2つの要求、オプションのプレフライト要求、および put 要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="e15a9-373">失敗し **`GetValues2 [DisableCors]`** た CORS 要求をトリガーするには、このボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="e15a9-374">ドキュメントに記載されているように、応答は200成功を返しますが、CORS 要求は行われません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="e15a9-375">[**コンソール**] タブを選択して、CORS エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="e15a9-376">ブラウザーによっては、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="e15a9-377">`'https://cors1.azurewebsites.net/api/values/GetValues2'`送信元からのフェッチへのアクセス `'https://cors3.azurewebsites.net'` が CORS ポリシーによってブロックされました。要求されたリソースに ' アクセス制御許可-発信元 ' ヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="e15a9-378">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="e15a9-379">CORS が有効なエンドポイントは、 [curl](https://curl.haxx.se/)、 [Fiddler](https://www.telerik.com/fiddler)、 [Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="e15a9-380">ツールを使用する場合、ヘッダーによって指定された要求の配信元は、要求を受信している `Origin` ホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="e15a9-381">ヘッダーの値に基づいて要求が*クロスオリジン*でない場合は、 `Origin` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="e15a9-382">CORS ミドルウェアが要求を処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="e15a9-383">CORS ヘッダーが応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="e15a9-384">次のコマンドは、を使用し `curl` て、情報を含む OPTIONS 要求を発行します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="e15a9-385">エンドポイントルーティングを使用した CORS のテスト [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="e15a9-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="e15a9-386">現在、を使用して、エンドポイントごとに CORS を有効にする `RequireCors` ことは、[自動プレフライト要求](#apf)をサポートして**いません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="e15a9-387">[エンドポイントルーティングを使用して CORS を有効に](#ecors)する次のコードについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="e15a9-388">`TodoItems1Controller`テスト用のエンドポイントは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="e15a9-389">配置された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[テストページ](https://cors1.azurewebsites.net/test?number=1)から、前のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="e15a9-390">エンドポイントがプレフライト要求に応答して応答するため、 **Delete [EnableCors** **] ボタンは**正常に終了し `[EnableCors]` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="e15a9-391">他のエンドポイントは失敗します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-391">The other endpoints fails.</span></span> <span data-ttu-id="e15a9-392">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)によってが送信されるため、 **GET**ボタンは失敗します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="e15a9-393">次の例は `TodoItems2Controller` 同様のエンドポイントを提供しますが、オプションの要求に応答するための明示的なコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="e15a9-394">配置されたサンプルの[テストページ](https://cors1.azurewebsites.net/test?number=2)から、前のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="e15a9-395">[**コントローラー** ] ドロップダウンリストで、[**プレフライト**] を選択し、[**コントローラーの設定**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="e15a9-396">エンドポイントに対するすべての CORS 呼び出しが `TodoItems2Controller` 成功します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e15a9-397">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e15a9-397">Additional resources</span></span>

* [<span data-ttu-id="e15a9-398">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="e15a9-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="e15a9-399">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="e15a9-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e15a9-400">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e15a9-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e15a9-401">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="e15a9-402">ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="e15a9-403">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-403">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="e15a9-404">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="e15a9-405">場合によっては、他のサイトがアプリに対してクロスオリジン要求を行うことを許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="e15a9-406">詳細については、「 [MOZILLA CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="e15a9-407">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="e15a9-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="e15a9-408">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="e15a9-409">は、CORS 緩和され security のセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="e15a9-410">CORS を許可すると、API の安全性が向上しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="e15a9-411">詳細については、「 [CORS のしくみ](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="e15a9-412">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="e15a9-413">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="e15a9-414">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e15a9-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="e15a9-415">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="e15a9-415">Same origin</span></span>

<span data-ttu-id="e15a9-416">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="e15a9-417">この2つの Url のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="e15a9-418">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="e15a9-419">`https://example.net`: 異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="e15a9-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="e15a9-420">`https://www.example.com/foo.html`: 異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="e15a9-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="e15a9-421">`http://example.com/foo.html`: 異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="e15a9-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="e15a9-422">`https://example.com:9000/foo.html`: 別のポート</span><span class="sxs-lookup"><span data-stu-id="e15a9-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="e15a9-423">Internet Explorer は、オリジンを比較するときにポートを考慮しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="e15a9-424">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="e15a9-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="e15a9-425">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="e15a9-426">次のコードでは、指定したオリジンのアプリ全体に対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="e15a9-427">上記のコードでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-427">The preceding code:</span></span>

* <span data-ttu-id="e15a9-428">ポリシー名を " \_ myallow固有のオリジン" に設定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="e15a9-429">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="e15a9-430"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>CORS を有効にする拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="e15a9-431"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="e15a9-432">ラムダはオブジェクトを受け取り <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="e15a9-433">などの[構成オプション](#cors-policy-options)に `WithOrigins` ついては、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="e15a9-434">メソッド呼び出しによって、 <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> アプリのサービスコンテナーに CORS サービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="e15a9-435">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="e15a9-436">メソッドは、 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 次のコードに示すようにメソッドを連結できます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="e15a9-437">注: URL の末尾にスラッシュ () を含めることは**できません** `/` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="e15a9-438">URL がで終了した場合 `/` 、比較はを返し、 `false` ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="e15a9-439">次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="e15a9-440">メモ: `UseCors` の前にを呼び出す必要があり `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="e15a9-441">ページ/コントローラー/アクションレベルで CORS ポリシーを適用するには[ Razor 、「ページ、コントローラー、およびアクションメソッドで Cors を有効](#ecors)にする」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-441">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="e15a9-442">前のコードのようなコードをテストする方法については、「[テスト CORS](#test) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="e15a9-443">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="e15a9-443">Enable CORS with attributes</span></span>

<span data-ttu-id="e15a9-444">[ &lbrack; Enablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用するための代替手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="e15a9-445">属性を指定すると `[EnableCors]` 、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS が有効になります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="e15a9-446">を使用し `[EnableCors]` て、既定のポリシーを指定し、 `[EnableCors("{Policy String}")]` ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="e15a9-447">`[EnableCors]`属性は次のものに適用できます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-447">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="e15a9-448">改`PageModel`</span><span class="sxs-lookup"><span data-stu-id="e15a9-448"> Page `PageModel`</span></span>
* <span data-ttu-id="e15a9-449">コントローラー</span><span class="sxs-lookup"><span data-stu-id="e15a9-449">Controller</span></span>
* <span data-ttu-id="e15a9-450">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="e15a9-450">Controller action method</span></span>

<span data-ttu-id="e15a9-451">属性を使用して、コントローラー/ページモデル/アクションに異なるポリシーを適用でき `[EnableCors]` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="e15a9-452">`[EnableCors]`属性がコントローラー/ページモデル/アクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、**両方**のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="e15a9-453">ポリシーを組み合わせることはお勧めし**ません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="e15a9-454">`[EnableCors]`両方では**なく**、属性またはミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-454">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="e15a9-455">を使用する場合は `[EnableCors]` 、既定のポリシーを定義**しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="e15a9-456">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="e15a9-457">次のコードでは、CORS の既定のポリシーとという名前のポリシーを作成し `"AnotherPolicy"` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="e15a9-458">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="e15a9-458">Disable CORS</span></span>

<span data-ttu-id="e15a9-459">[ &lbrack; Disablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラー/ページモデル/アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="e15a9-460">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="e15a9-460">CORS policy options</span></span>

<span data-ttu-id="e15a9-461">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="e15a9-462">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="e15a9-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="e15a9-463">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="e15a9-464">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="e15a9-465">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="e15a9-466">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="e15a9-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="e15a9-467">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="e15a9-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>はで呼び出され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e15a9-469">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="e15a9-470">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="e15a9-470">Set the allowed origins</span></span>

<span data-ttu-id="e15a9-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: 任意のスキーム (または) を持つすべてのオリジンからの CORS 要求を許可 `http` `https` します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="e15a9-472">`AllowAnyOrigin`*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e15a9-473">とを指定すると、 `AllowAnyOrigin` `AllowCredentials` 安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e15a9-474">セキュリティで保護されたアプリの場合は、クライアントがサーバーリソースへのアクセスを承認する必要がある場合は、オリジンの正確な一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="e15a9-475">`AllowAnyOrigin`プレフライト要求とヘッダーに影響し `Access-Control-Allow-Origin` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="e15a9-476">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e15a9-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインとオリジンが一致するようにするための関数として、ポリシーのプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="e15a9-478">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="e15a9-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="e15a9-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="e15a9-480">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="e15a9-481">プレフライト要求とヘッダーに影響し `Access-Control-Allow-Methods` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="e15a9-482">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="e15a9-483">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-483">Set the allowed request headers</span></span>

<span data-ttu-id="e15a9-484">CORS 要求で特定のヘッダーを送信できるようにするには、 *author 要求ヘッダー*と呼ばれるを呼び出し、 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-484">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e15a9-485">すべての作成者要求ヘッダーを許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e15a9-486">この設定は、プレフライト要求とヘッダーに影響し `Access-Control-Request-Headers` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="e15a9-487">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e15a9-488">CORS ミドルウェアでは、 `Access-Control-Request-Headers` CorsPolicy で構成されている値に関係なく、常にの4つのヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="e15a9-489">このヘッダーの一覧には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="e15a9-490">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="e15a9-491">は常に許可されているため、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求に正常に応答し `Content-Language` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="e15a9-492">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-492">Set the exposed response headers</span></span>

<span data-ttu-id="e15a9-493">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="e15a9-494">詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="e15a9-495">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="e15a9-496">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-496">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="e15a9-497">アプリで他のヘッダーを使用できるようにするには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="e15a9-498">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="e15a9-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="e15a9-499">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="e15a9-500">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="e15a9-501">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-501">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="e15a9-502">クロスオリジン要求で資格情報を送信するには、クライアントがに設定されている必要があり `XMLHttpRequest.withCredentials` `true` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="e15a9-503">直接の使用 `XMLHttpRequest` :</span><span class="sxs-lookup"><span data-stu-id="e15a9-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="e15a9-504">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="e15a9-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="e15a9-505">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="e15a9-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="e15a9-506">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-506">The server must allow the credentials.</span></span> <span data-ttu-id="e15a9-507">クロスオリジンの資格情報を許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="e15a9-508">HTTP 応答には、 `Access-Control-Allow-Credentials` サーバーがクロスオリジン要求に対して資格情報を許可することをブラウザーに示すヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e15a9-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="e15a9-509">ブラウザーが資格情報を送信しても、応答に有効なヘッダーが含まれていない場合 `Access-Control-Allow-Credentials` 、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="e15a9-510">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="e15a9-511">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="e15a9-512">また、CORS 仕様では、ヘッダーが存在する場合、[オリジン] を [(すべてのオリジン)] に設定することもでき `"*"` `Access-Control-Allow-Credentials` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="e15a9-513">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="e15a9-513">Preflight requests</span></span>

<span data-ttu-id="e15a9-514">一部の CORS 要求については、ブラウザーは実際の要求を行う前に追加の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="e15a9-515">この要求は*プレフライト要求*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-515">This request is called a *preflight request*.</span></span> <span data-ttu-id="e15a9-516">次の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="e15a9-517">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="e15a9-518">アプリで `Accept` は、、、 `Accept-Language` `Content-Language` 、 `Content-Type` 、または `Last-Event-ID` 以外の要求ヘッダーは設定されません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="e15a9-519">ヘッダーには、設定されて `Content-Type` いる場合、次のいずれかの値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="e15a9-520">クライアント要求に対して設定された要求ヘッダーに適用される規則は、オブジェクトに対してを呼び出すことによって、アプリが設定するヘッダーに適用され `setRequestHeader` `XMLHttpRequest` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="e15a9-521">CORS 仕様は、これらのヘッダー*作成者要求ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-521">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="e15a9-522">このルールは、ブラウザーが設定できるヘッダー (、、など) には適用されません `User-Agent` `Host` `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="e15a9-523">プレフライト要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="e15a9-524">事前フライト要求では、HTTP OPTIONS メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="e15a9-525">次の2つの特殊なヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-525">It includes two special headers:</span></span>

* <span data-ttu-id="e15a9-526">`Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="e15a9-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="e15a9-527">`Access-Control-Request-Headers`: アプリが実際の要求に対して設定する要求ヘッダーのリスト。</span><span class="sxs-lookup"><span data-stu-id="e15a9-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="e15a9-528">前述のように、これにはブラウザーが設定するヘッダー (など) は含まれません `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="e15a9-529">CORS が適切なポリシーで有効になっている場合、ASP.NET Core は通常、CORS プレフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="e15a9-530">[プレフライト要求の場合は、[HttpOptions] 属性を](#pro)参照してください。</span><span class="sxs-lookup"><span data-stu-id="e15a9-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="e15a9-531">CORS のプレフライト要求には、 `Access-Control-Request-Headers` 実際の要求と共に送信されるヘッダーをサーバーに示すヘッダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="e15a9-532">特定のヘッダーを許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e15a9-533">すべての作成者要求ヘッダーを許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e15a9-534">ブラウザーの設定方法が完全に一致しているわけではありません `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="e15a9-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="e15a9-535">ヘッダーを以外の任意の値 (またはを使用) に設定する場合は、少なくとも、、、 `"*"` <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> `Accept` `Content-Type` `Origin` 、およびサポートするカスタムヘッダーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="e15a9-536">プレフライト要求に対する応答の例を次に示します (サーバーが要求を許可していることを前提としています)。</span><span class="sxs-lookup"><span data-stu-id="e15a9-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="e15a9-537">応答には、 `Access-Control-Allow-Methods` 許可されているメソッドと、必要に応じてヘッダーを一覧表示するヘッダーが含まれてい `Access-Control-Allow-Headers` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="e15a9-538">プレフライト要求が成功した場合、ブラウザーは実際の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="e15a9-539">プレフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e15a9-540">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="e15a9-541">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="e15a9-541">Set the preflight expiration time</span></span>

<span data-ttu-id="e15a9-542">ヘッダーは、 `Access-Control-Max-Age` プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="e15a9-543">このヘッダーを設定するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="e15a9-544">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="e15a9-544">How CORS works</span></span>

<span data-ttu-id="e15a9-545">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="e15a9-546">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="e15a9-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="e15a9-547">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="e15a9-548">たとえば、悪意のあるアクターがを使用してサイトに対して[クロスサイトスクリプティング (XSS) を防止](xref:security/cross-site-scripting)し、CORS が有効になっているサイトに対してクロスサイト要求を実行して情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="e15a9-549">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="e15a9-550">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="e15a9-551">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="e15a9-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="e15a9-552">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="e15a9-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="e15a9-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="e15a9-554">Postman</span><span class="sxs-lookup"><span data-stu-id="e15a9-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="e15a9-555">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="e15a9-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="e15a9-556">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="e15a9-557">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="e15a9-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="e15a9-558">(CORS を使用しない) ブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="e15a9-559">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="e15a9-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="e15a9-560">JSONP は XHR を使用しないので、タグを使用して `<script>` 応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="e15a9-561">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="e15a9-562">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="e15a9-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="e15a9-563">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="e15a9-564">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="e15a9-565">次に、クロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="e15a9-566">ヘッダーは、 `Origin` 要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="e15a9-567">`Origin`ヘッダーは必須であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="e15a9-568">サーバーが要求を許可している場合は、応答でヘッダーが設定され `Access-Control-Allow-Origin` ます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="e15a9-569">このヘッダーの値は、要求のヘッダーと一致するか、 `Origin` またはワイルドカード値です `"*"` 。つまり、すべての配信元が許可されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="e15a9-570">応答にヘッダーが含まれていない場合 `Access-Control-Allow-Origin` 、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="e15a9-571">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="e15a9-572">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="e15a9-573">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="e15a9-573">Test CORS</span></span>

<span data-ttu-id="e15a9-574">CORS をテストするには:</span><span class="sxs-lookup"><span data-stu-id="e15a9-574">To test CORS:</span></span>

1. <span data-ttu-id="e15a9-575">[API プロジェクトを作成](xref:tutorials/first-web-api)します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="e15a9-576">または、[サンプルをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="e15a9-577">このドキュメントのいずれかの方法を使用して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="e15a9-578">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="e15a9-579">`WithOrigins("https://localhost:<port>");`サンプルアプリのテストにのみ使用してください[サンプルコードをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="e15a9-580">Web アプリプロジェクトを作成 Razor します (ページまたは MVC)。</span><span class="sxs-lookup"><span data-stu-id="e15a9-580">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="e15a9-581">このサンプルでは、 Razor ページを使用します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-581">The sample uses Razor Pages.</span></span> <span data-ttu-id="e15a9-582">API プロジェクトと同じソリューションに web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="e15a9-583">次の強調表示されたコードを*インデックスの cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="e15a9-584">前のコードで、をデプロイされた `url: 'https://<web app>.azurewebsites.net/api/values/1',` アプリの URL に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="e15a9-585">API プロジェクトをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-585">Deploy the API project.</span></span> <span data-ttu-id="e15a9-586">たとえば、 [Azure にデプロイ](xref:host-and-deploy/azure-apps/index)します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="e15a9-587">Razorデスクトップからページまたは MVC アプリを実行し、[**テスト**] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-587">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="e15a9-588">F12 ツールを使用して、エラーメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="e15a9-589">から localhost の配信元を削除 `WithOrigins` し、アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="e15a9-590">または、別のポートを使用してクライアントアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="e15a9-591">たとえば、Visual Studio からを実行します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="e15a9-592">クライアントアプリでテストします。</span><span class="sxs-lookup"><span data-stu-id="e15a9-592">Test with the client app.</span></span> <span data-ttu-id="e15a9-593">CORS エラーはエラーを返しますが、エラーメッセージは JavaScript では使用できません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="e15a9-594">F12 ツールの [コンソール] タブを使用して、エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="e15a9-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="e15a9-595">ブラウザーによっては、次のようなエラー (F12 ツールコンソール) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="e15a9-596">Microsoft Edge を使用する:</span><span class="sxs-lookup"><span data-stu-id="e15a9-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="e15a9-597">**SEC7120: [CORS] オリジンは、次の `https://localhost:44375` `https://localhost:44375` 場所にあるクロスオリジンリソースのアクセス制御-許可-オリジン応答ヘッダーで見つかりませんでした`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="e15a9-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="e15a9-598">Chrome を使用する:</span><span class="sxs-lookup"><span data-stu-id="e15a9-598">Using Chrome:</span></span>

     <span data-ttu-id="e15a9-599">**`https://webapi.azurewebsites.net/api/values/1`オリジンからの XMLHttpRequest へのアクセス `https://localhost:44375` が CORS ポリシーによってブロックされています。要求されたリソースに ' アクセス制御許可-発信元 ' ヘッダーが存在しません。**</span><span class="sxs-lookup"><span data-stu-id="e15a9-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="e15a9-600">CORS が有効なエンドポイントは、 [Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="e15a9-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="e15a9-601">ツールを使用する場合、ヘッダーによって指定された要求の配信元は、要求を受信している `Origin` ホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="e15a9-602">ヘッダーの値に基づいて要求が*クロスオリジン*でない場合は、 `Origin` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="e15a9-603">CORS ミドルウェアが要求を処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="e15a9-604">CORS ヘッダーが応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="e15a9-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="e15a9-605">IIS での CORS</span><span class="sxs-lookup"><span data-stu-id="e15a9-605">CORS in IIS</span></span>

<span data-ttu-id="e15a9-606">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合、CORS は Windows 認証の前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="e15a9-607">このシナリオをサポートするには、アプリ用に[IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールして構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e15a9-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e15a9-608">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e15a9-608">Additional resources</span></span>

* [<span data-ttu-id="e15a9-609">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="e15a9-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="e15a9-610">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="e15a9-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
