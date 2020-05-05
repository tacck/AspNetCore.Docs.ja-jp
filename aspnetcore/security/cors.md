---
title: ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする
author: rick-anderson
description: ASP.NET Core アプリでのクロスオリジン要求を許可または拒否するための標準として CORS を使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 6f523a21fe8119c2e4ca4f751ac5b6abc686404b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773812"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="c3103-103">ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする</span><span class="sxs-lookup"><span data-stu-id="c3103-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c3103-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c3103-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="c3103-105">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="c3103-106">ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="c3103-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="c3103-107">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="c3103-108">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="c3103-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c3103-109">場合によっては、他のサイトがアプリに対してクロスオリジン要求を行うことを許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="c3103-110">詳細については、「 [MOZILLA CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="c3103-111">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="c3103-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="c3103-112">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="c3103-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="c3103-113">は、CORS 緩和され security のセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="c3103-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="c3103-114">CORS を許可すると、API の安全性が向上しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="c3103-115">詳細については、「 [CORS のしくみ](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="c3103-116">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="c3103-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="c3103-117">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="c3103-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="c3103-118">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c3103-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="c3103-119">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="c3103-119">Same origin</span></span>

<span data-ttu-id="c3103-120">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="c3103-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="c3103-121">この2つの Url のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="c3103-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="c3103-122">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="c3103-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="c3103-123">`https://example.net`&ndash;異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="c3103-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="c3103-124">`https://www.example.com/foo.html`&ndash;異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="c3103-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="c3103-125">`http://example.com/foo.html`&ndash;異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="c3103-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="c3103-126">`https://example.com:9000/foo.html`&ndash;別のポート</span><span class="sxs-lookup"><span data-stu-id="c3103-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="c3103-127">CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="c3103-127">Enable CORS</span></span>

<span data-ttu-id="c3103-128">CORS を有効にするには、次の3つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="c3103-129">[名前付きポリシー](#np)または既定の[ポリシー](#dp)を使用するミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="c3103-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="c3103-130">[エンドポイントルーティング](#ecors)を使用する。</span><span class="sxs-lookup"><span data-stu-id="c3103-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="c3103-131">[[Enablecors]](#attr)属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="c3103-132">名前付きポリシーで[[Enablecors]](#attr)属性を使用すると、CORS をサポートするエンドポイントを制限するための最も細かい制御が可能になります。</span><span class="sxs-lookup"><span data-stu-id="c3103-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="c3103-133">各方法の詳細については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="c3103-134">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="c3103-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="c3103-135">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="c3103-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="c3103-136">次のコードは、指定されたオリジンを持つすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="c3103-137">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c3103-137">The preceding code:</span></span>

* <span data-ttu-id="c3103-138">ポリシー名をに`_myAllowSpecificOrigins`設定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="c3103-139">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="c3103-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="c3103-140"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>拡張メソッドを呼び出し、 `_myAllowSpecificOrigins` CORS ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="c3103-141">`UseCors`CORS ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="c3103-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="c3103-142">へ`UseCors`の呼び出しは、の後`UseRouting`、の前`UseAuthorization`に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="c3103-143">詳細については、「[ミドルウェアの順序](xref:fundamentals/middleware/index#middleware-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="c3103-144"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="c3103-145">ラムダはオブジェクトを<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c3103-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="c3103-146">などの[構成オプション](#cors-policy-options)に`WithOrigins`ついては、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="c3103-147">すべての`_myAllowSpecificOrigins`コントローラーエンドポイントに対して CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="c3103-148">特定のエンドポイントに CORS ポリシーを適用するには、「[エンドポイントルーティング](#ecors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="c3103-149">エンドポイントルーティングでは、CORS ミドルウェアを`UseRouting`と`UseEndpoints`の呼び出しの間で実行するように構成する***必要があり***ます。</span><span class="sxs-lookup"><span data-stu-id="c3103-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="c3103-150">前のコードのようなコードをテストする方法については、「[テスト CORS](#testc) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="c3103-151">メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しによって、アプリのサービスコンテナーに CORS サービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="c3103-152">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="c3103-153">メソッド<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>は、次のコードに示すように、連結できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="c3103-154">注: 指定された URL に末尾にスラッシュ (`/`) を含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="c3103-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="c3103-155">URL がで`/`終了した場合、比較`false`はを返し、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="c3103-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="c3103-156">既定のポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="c3103-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="c3103-157">次の強調表示されたコードは、既定の CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="c3103-158">上記のコードは、すべてのコントローラーエンドポイントに既定の CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="c3103-159">エンドポイント ルーティングで CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="c3103-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="c3103-160">現在、を使用して、エンドポイント`RequireCors`ごとに CORS を有効にすることは、[自動プレフライト要求](#apf)をサポートして***いません***。</span><span class="sxs-lookup"><span data-stu-id="c3103-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="c3103-161">詳細については、こちらの[GitHub の問題](https://github.com/dotnet/aspnetcore/issues/20709)を参照してください。また、[エンドポイントルーティングと [HttpOptions] を使用](#tcer)して CORS をテストしてください。</span><span class="sxs-lookup"><span data-stu-id="c3103-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="c3103-162">エンドポイントルーティングを使用すると、 <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*>一連の拡張メソッドを使用して、エンドポイントごとに CORS を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="c3103-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="c3103-163">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c3103-163">In the preceding code:</span></span>

* <span data-ttu-id="c3103-164">`app.UseCors`CORS ミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="c3103-165">既定のポリシーが構成されて`app.UseCors()`いないため、単独で CORS を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="c3103-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="c3103-166">および`/echo`コントローラーエンドポイントは、指定されたポリシーを使用して、クロスオリジン要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="c3103-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="c3103-167">既定`/echo2`のRazorポリシーが指定されていないため、と Pages エンドポイントはクロスオリジン要求を許可し***ません***。</span><span class="sxs-lookup"><span data-stu-id="c3103-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="c3103-168">[[Disablecors]](#dc)属性では、を使用`RequireCors`してエンドポイントルーティングによって有効にされた CORS は無効になり***ません***。</span><span class="sxs-lookup"><span data-stu-id="c3103-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="c3103-169">前述のようなコードをテストする方法については、「[エンドポイントルーティングを使用した CORS のテスト」および「[HttpOptions]](#tcer) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="c3103-170">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="c3103-170">Enable CORS with attributes</span></span>

<span data-ttu-id="c3103-171">[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性を使用して cors を有効にし、cors を必要とするエンドポイントのみに名前付きポリシーを適用することで、最も細かい制御が可能になります。</span><span class="sxs-lookup"><span data-stu-id="c3103-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="c3103-172">[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用するための代替手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="c3103-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="c3103-173">属性`[EnableCors]`は、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="c3103-174">`[EnableCors]`既定のポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="c3103-175">`[EnableCors("{Policy String}")]`名前付きポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="c3103-176">属性`[EnableCors]`は次のものに適用できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-176">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="c3103-177">改`PageModel`</span><span class="sxs-lookup"><span data-stu-id="c3103-177"> Page `PageModel`</span></span>
* <span data-ttu-id="c3103-178">コントローラー</span><span class="sxs-lookup"><span data-stu-id="c3103-178">Controller</span></span>
* <span data-ttu-id="c3103-179">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="c3103-179">Controller action method</span></span>

<span data-ttu-id="c3103-180">`[EnableCors]`属性を使用して、さまざまなポリシーをコントローラー、ページモデル、またはアクションメソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="c3103-181">`[EnableCors]`属性がコントローラー、ページモデル、またはアクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、***両方***のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="c3103-182">***ポリシーを組み合わせることはお勧めしません。*** 同じアプリ***内ではなく、属性またはミドルウェアを使用します。*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="c3103-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="c3103-183">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="c3103-184">次のコードでは、2つの CORS ポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3103-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="c3103-185">CORS 要求を制限する最も細かい制御:</span><span class="sxs-lookup"><span data-stu-id="c3103-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="c3103-186">名前`[EnableCors("MyPolicy")]`付きポリシーでを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="c3103-187">既定のポリシーは定義しないでください。</span><span class="sxs-lookup"><span data-stu-id="c3103-187">Don't define a default policy.</span></span>
* <span data-ttu-id="c3103-188">[エンドポイントルーティング](#ecors)を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="c3103-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="c3103-189">次のセクションのコードは、上記のリストを満たしています。</span><span class="sxs-lookup"><span data-stu-id="c3103-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="c3103-190">前のコードのようなコードをテストする方法については、「[テスト CORS](#testc) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="c3103-191">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="c3103-191">Disable CORS</span></span>

<span data-ttu-id="c3103-192">[[Disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、[エンドポイントルーティング](#ecors)によって有効にされた CORS を無効にし***ません***。</span><span class="sxs-lookup"><span data-stu-id="c3103-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="c3103-193">次のコードでは、CORS `"MyPolicy"`ポリシーを定義しています。</span><span class="sxs-lookup"><span data-stu-id="c3103-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="c3103-194">次のコードは、 `GetValues2`アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="c3103-195">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c3103-195">The preceding code:</span></span>

* <span data-ttu-id="c3103-196">[エンドポイントルーティング](#ecors)で CORS を有効にしません。</span><span class="sxs-lookup"><span data-stu-id="c3103-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="c3103-197">[既定の CORS ポリシー](#dp)は定義されていません。</span><span class="sxs-lookup"><span data-stu-id="c3103-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="c3103-198">では、 [[Enablecors ("MyPolicy")]](#attr)を`"MyPolicy"`使用して、コントローラーの CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="c3103-199">`GetValues2`メソッドの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="c3103-200">前のコードをテストする手順については、「[テスト CORS](#testc) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="c3103-201">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="c3103-201">CORS policy options</span></span>

<span data-ttu-id="c3103-202">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="c3103-203">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="c3103-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="c3103-204">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="c3103-205">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="c3103-206">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="c3103-207">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="c3103-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="c3103-208">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="c3103-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>はで`Startup.ConfigureServices`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c3103-210">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="c3103-211">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="c3103-211">Set the allowed origins</span></span>

<span data-ttu-id="c3103-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;任意のスキーム (`http`または`https`) を持つすべてのオリジンからの CORS 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="c3103-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="c3103-213">`AllowAnyOrigin`*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="c3103-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="c3103-214">と`AllowAnyOrigin` `AllowCredentials`を指定すると、安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="c3103-215">アプリが両方の方法で構成されている場合、CORS サービスは無効な CORS 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="c3103-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="c3103-216">`AllowAnyOrigin`プレフライト要求とヘッダー `Access-Control-Allow-Origin`に影響します。</span><span class="sxs-lookup"><span data-stu-id="c3103-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="c3103-217">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c3103-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;ポリシーの<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>プロパティを、オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインと一致させることができるようにする関数に設定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="c3103-219">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="c3103-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="c3103-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="c3103-221">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="c3103-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="c3103-222">プレフライト要求とヘッダー `Access-Control-Allow-Methods`に影響します。</span><span class="sxs-lookup"><span data-stu-id="c3103-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="c3103-223">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="c3103-224">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-224">Set the allowed request headers</span></span>

<span data-ttu-id="c3103-225">CORS 要求で特定のヘッダーを送信できるようにするには、 [author 要求ヘッダー](https://xhr.spec.whatwg.org/#request)と呼ばれるを呼び出し<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 、許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="c3103-226">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>するには、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="c3103-227">`AllowAnyHeader`プレフライト要求および[アクセス制御-要求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)ヘッダーヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="c3103-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="c3103-228">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c3103-229">によって指定された特定のヘッダー `WithHeaders`に対して CORS ミドルウェアポリシーが一致`Access-Control-Request-Headers`するのは、ヘッダーが`WithHeaders`で記述されているヘッダーと完全に一致する場合のみです。</span><span class="sxs-lookup"><span data-stu-id="c3103-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="c3103-230">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c3103-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="c3103-231">CORS ミドルウェアは、次の要求ヘッダーを使用して`Content-Language`プレフライト要求を拒否します。 ([headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) は、に`WithHeaders`は記載されていないためです。</span><span class="sxs-lookup"><span data-stu-id="c3103-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="c3103-232">アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="c3103-233">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="c3103-234">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-234">Set the exposed response headers</span></span>

<span data-ttu-id="c3103-235">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="c3103-236">詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="c3103-237">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3103-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="c3103-238">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="c3103-239">アプリで他のヘッダーを使用できるように<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>するには、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="c3103-240">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="c3103-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="c3103-241">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="c3103-242">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="c3103-243">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="c3103-244">クロスオリジン要求で資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials`に`true`設定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="c3103-245">直接`XMLHttpRequest`の使用:</span><span class="sxs-lookup"><span data-stu-id="c3103-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="c3103-246">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="c3103-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="c3103-247">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="c3103-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="c3103-248">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-248">The server must allow the credentials.</span></span> <span data-ttu-id="c3103-249">クロスオリジンの資格情報を許可する<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>には、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="c3103-250">HTTP 応答には、 `Access-Control-Allow-Credentials`サーバーがクロスオリジン要求に対して資格情報を許可することをブラウザーに示すヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3103-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="c3103-251">ブラウザーが資格情報を送信しても、応答に`Access-Control-Allow-Credentials`有効なヘッダーが含まれていない場合、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="c3103-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="c3103-252">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="c3103-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="c3103-253">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="c3103-254">また、CORS 仕様では、 `"*"` `Access-Control-Allow-Credentials`ヘッダーが存在する場合、[オリジン] を [(すべてのオリジン)] に設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="c3103-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="c3103-255">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="c3103-255">Preflight requests</span></span>

<span data-ttu-id="c3103-256">一部の CORS 要求については、ブラウザーは、実際の要求を行う前に、追加の[オプション](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="c3103-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="c3103-257">この要求は[プレフライト要求](https://developer.mozilla.org/docs/Glossary/Preflight_request)と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="c3103-258">次の***すべて***の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="c3103-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="c3103-259">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="c3103-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="c3103-260">`Accept`アプリでは、 `Accept-Language`、、 `Content-Language`、 `Content-Type`、または以外の要求`Last-Event-ID`ヘッダーは設定されません。</span><span class="sxs-lookup"><span data-stu-id="c3103-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="c3103-261">ヘッダー `Content-Type`には、設定されている場合、次のいずれかの値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="c3103-262">クライアント要求に対して設定された要求ヘッダーに適用される規則は、 `setRequestHeader` `XMLHttpRequest`オブジェクトに対してを呼び出すことによって、アプリが設定するヘッダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="c3103-263">CORS 仕様は、これらのヘッダー[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="c3103-264">このルールは`User-Agent`、ブラウザーが設定できるヘッダー (、 `Host`、など) には`Content-Length`適用されません。</span><span class="sxs-lookup"><span data-stu-id="c3103-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="c3103-265">次に示すのは、このドキュメントの「[テスト CORS](#testc) 」セクションの **[Put test]** ボタンから行ったプレフライト要求に似た応答の例です。</span><span class="sxs-lookup"><span data-stu-id="c3103-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="c3103-266">プレフライト要求では、 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="c3103-267">次のヘッダーが含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-267">It may include the following headers:</span></span>

* <span data-ttu-id="c3103-268">[アクセス制御要求メソッド](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="c3103-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="c3103-269">[アクセス制御要求ヘッダー](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): アプリが実際の要求に対して設定する要求ヘッダーのリスト。</span><span class="sxs-lookup"><span data-stu-id="c3103-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="c3103-270">前述のように、これにはブラウザーが設定するヘッダー (など`User-Agent`) は含まれません。</span><span class="sxs-lookup"><span data-stu-id="c3103-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="c3103-271">アクセス制御-許可-メソッド</span><span class="sxs-lookup"><span data-stu-id="c3103-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="c3103-272">プレフライト要求が拒否された場合、アプリ`200 OK`は応答を返しますが、CORS ヘッダーは設定しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="c3103-273">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="c3103-274">拒否されたプレフライト要求の例については、このドキュメントの「[テスト CORS](#testc) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="c3103-275">F12 ツールを使用すると、コンソールアプリには、ブラウザーに応じて次のいずれかのようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="c3103-276">Firefox: クロスオリジン要求がブロックされています: 同じオリジンポリシーで、 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`でのリモートリソースの読み取りが許可されていません。</span><span class="sxs-lookup"><span data-stu-id="c3103-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="c3103-277">(理由: CORS 要求が失敗しました)。</span><span class="sxs-lookup"><span data-stu-id="c3103-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="c3103-278">詳細情報</span><span class="sxs-lookup"><span data-stu-id="c3103-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="c3103-279">Chromium ベース: オリジン 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5https://cors3.azurewebsites.net' からの ' ' でのフェッチへのアクセスが CORS ポリシーによってブロックされました。プレフライト要求への応答がアクセス制御チェックに合格しません: 要求されたリソースに ' アクセス制御-許可-オリジン ' ヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="c3103-280">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="c3103-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="c3103-281">特定のヘッダーを許可する<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>には、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="c3103-282">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>するには、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="c3103-283">ブラウザーの設定`Access-Control-Request-Headers`方法が一貫していません。</span><span class="sxs-lookup"><span data-stu-id="c3103-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="c3103-284">次のいずれかの場合:</span><span class="sxs-lookup"><span data-stu-id="c3103-284">If either:</span></span>

* <span data-ttu-id="c3103-285">ヘッダーは、`"*"`</span><span class="sxs-lookup"><span data-stu-id="c3103-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="c3103-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>が呼び出されます: 少なく`Accept` `Content-Type`とも、 `Origin`、、、およびサポートするカスタムヘッダーを含めます。</span><span class="sxs-lookup"><span data-stu-id="c3103-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="c3103-287">自動プレフライト要求コード</span><span class="sxs-lookup"><span data-stu-id="c3103-287">Automatic preflight request code</span></span>

<span data-ttu-id="c3103-288">CORS ポリシーを適用する場合は、次のいずれかの方法を実行します。</span><span class="sxs-lookup"><span data-stu-id="c3103-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="c3103-289">で`Startup.Configure`を呼び`app.UseCors`出すことにより、グローバルに。</span><span class="sxs-lookup"><span data-stu-id="c3103-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="c3103-290">`[EnableCors]`属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="c3103-291">ASP.NET Core は、プレフライトオプション要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="c3103-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="c3103-292">現在、を使用して、エンドポイント`RequireCors`ごとに CORS を有効にすることは、自動プレフライト要求をサポートして***いません***。</span><span class="sxs-lookup"><span data-stu-id="c3103-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="c3103-293">このドキュメントの「[テスト CORS](#testc) 」セクションでは、この動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="c3103-294">プレフライト要求の [HttpOptions] 属性</span><span class="sxs-lookup"><span data-stu-id="c3103-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="c3103-295">CORS が適切なポリシーで有効になっている場合、ASP.NET Core は通常、CORS プレフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="c3103-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="c3103-296">シナリオによっては、これが当てはまりません。</span><span class="sxs-lookup"><span data-stu-id="c3103-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="c3103-297">たとえば、CORS を[エンドポイントルーティングと共](#ecors)に使用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="c3103-298">次のコードでは、 [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)属性を使用して、OPTIONS 要求のエンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3103-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="c3103-299">前のコードをテストする手順については、「[エンドポイントルーティングを使用した CORS のテスト」および「[HttpOptions]](#tcer) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="c3103-300">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-300">Set the preflight expiration time</span></span>

<span data-ttu-id="c3103-301">ヘッダー `Access-Control-Max-Age`は、プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="c3103-302">このヘッダーを設定するに<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>は、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="c3103-303">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="c3103-303">How CORS works</span></span>

<span data-ttu-id="c3103-304">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="c3103-305">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="c3103-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="c3103-306">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="c3103-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="c3103-307">たとえば、悪意のあるアクターがサイトに対して[クロスサイトスクリプティング (XSS)](xref:security/cross-site-scripting)を使用して、CORS が有効になっているサイトに対してクロスサイト要求を実行し、情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="c3103-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="c3103-308">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="c3103-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="c3103-309">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="c3103-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="c3103-310">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="c3103-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="c3103-311">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="c3103-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="c3103-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="c3103-313">Postman</span><span class="sxs-lookup"><span data-stu-id="c3103-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="c3103-314">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="c3103-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="c3103-315">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="c3103-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="c3103-316">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="c3103-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="c3103-317">CORS のないブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="c3103-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="c3103-318">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="c3103-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="c3103-319">JSONP は XHR を使用しないの`<script>`で、タグを使用して応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="c3103-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="c3103-320">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="c3103-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="c3103-321">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="c3103-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="c3103-322">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="c3103-323">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="c3103-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="c3103-324">配置された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の [[テストの配置] ボタン](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="c3103-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="c3103-325">次に、[[値](https://cors3.azurewebsites.net/)のテスト] ボタンからへ`https://cors1.azurewebsites.net/api/values`のクロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="c3103-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="c3103-326">`Origin`ヘッダー:</span><span class="sxs-lookup"><span data-stu-id="c3103-326">The `Origin` header:</span></span>

* <span data-ttu-id="c3103-327">要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="c3103-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="c3103-328">は必須であり、ホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="c3103-329">**一般的なヘッダー**</span><span class="sxs-lookup"><span data-stu-id="c3103-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="c3103-330">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="c3103-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="c3103-331">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="c3103-331">**Request headers**</span></span>

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

<span data-ttu-id="c3103-332">要求`OPTIONS`では、サーバーは応答**のヘッダー** `Access-Control-Allow-Origin: {allowed origin}`ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="c3103-333">たとえば、デプロイされた[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[Delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS`要求には、次のヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3103-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="c3103-334">**一般的なヘッダー**</span><span class="sxs-lookup"><span data-stu-id="c3103-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="c3103-335">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="c3103-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="c3103-336">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="c3103-336">**Request headers**</span></span>

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

<span data-ttu-id="c3103-337">上記の**応答ヘッダー**では、サーバーは応答で[アクセス制御-許可](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="c3103-338">この`https://cors1.azurewebsites.net`ヘッダーの値は、要求`Origin`のヘッダーと一致します。</span><span class="sxs-lookup"><span data-stu-id="c3103-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="c3103-339"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>が呼び出される`Access-Control-Allow-Origin: *`と、ワイルドカード値が返されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="c3103-340">`AllowAnyOrigin`任意の発信元を許可します。</span><span class="sxs-lookup"><span data-stu-id="c3103-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="c3103-341">応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="c3103-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="c3103-342">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="c3103-343">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="c3103-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="c3103-344">表示オプションの要求</span><span class="sxs-lookup"><span data-stu-id="c3103-344">Display OPTIONS requests</span></span>

<span data-ttu-id="c3103-345">既定では、Chrome および Edge ブラウザーでは、F12 ツールの [ネットワーク] タブの [要求] オプションが表示されません。</span><span class="sxs-lookup"><span data-stu-id="c3103-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="c3103-346">これらのブラウザーでオプションの要求を表示するには:</span><span class="sxs-lookup"><span data-stu-id="c3103-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="c3103-347">`chrome://flags/#out-of-blink-cors` または `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="c3103-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="c3103-348">フラグを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-348">disable the flag.</span></span>
* <span data-ttu-id="c3103-349">[再起動] をタップします。</span><span class="sxs-lookup"><span data-stu-id="c3103-349">restart.</span></span>

<span data-ttu-id="c3103-350">既定では、Firefox によってオプションの要求が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="c3103-351">IIS での CORS</span><span class="sxs-lookup"><span data-stu-id="c3103-351">CORS in IIS</span></span>

<span data-ttu-id="c3103-352">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合、CORS は Windows 認証の前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="c3103-353">このシナリオをサポートするには、アプリ用に[IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールして構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="c3103-354">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="c3103-354">Test CORS</span></span>

<span data-ttu-id="c3103-355">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)には、CORS をテストするコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3103-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="c3103-356">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="c3103-357">このサンプルは、ページが追加Razorされた API プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="c3103-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="c3103-358">`WithOrigins("https://localhost:<port>");`サンプルアプリのテストにのみ使用してください[サンプルコードをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)します。</span><span class="sxs-lookup"><span data-stu-id="c3103-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="c3103-359">テスト用`ValuesController`のエンドポイントは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3103-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="c3103-360">[Mydisplayrouteinfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)は、 [Rick](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet パッケージによって提供され、ルート情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="c3103-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="c3103-361">次のいずれかの方法を使用して、上記のサンプルコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="c3103-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="c3103-362">で[https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)デプロイされたサンプルアプリを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="c3103-363">サンプルをダウンロードする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c3103-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="c3103-364">の既定の`https://localhost:5001`URL `dotnet run`を使用して、サンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="c3103-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="c3103-365">URL に対してポートを44398に設定して、Visual Studio からサンプル`https://localhost:44398`を実行します。</span><span class="sxs-lookup"><span data-stu-id="c3103-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="c3103-366">F12 ツールでブラウザーを使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c3103-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="c3103-367">[**値**] をクリックし、[**ネットワーク**] タブでヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="c3103-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="c3103-368">[**テストの配置**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c3103-368">Select the **PUT test** button.</span></span> <span data-ttu-id="c3103-369">OPTIONS 要求を表示する方法については、「[表示オプションの要求](#options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="c3103-370">**Put テスト**では、2つの要求、オプションのプレフライト要求、および put 要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="c3103-371">失敗し**`GetValues2 [DisableCors]`** た CORS 要求をトリガーするには、このボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c3103-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="c3103-372">ドキュメントに記載されているように、応答は200成功を返しますが、CORS 要求は行われません。</span><span class="sxs-lookup"><span data-stu-id="c3103-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="c3103-373">[**コンソール**] タブを選択して、CORS エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="c3103-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="c3103-374">ブラウザーによっては、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="c3103-375">送信元`'https://cors1.azurewebsites.net/api/values/GetValues2'` `'https://cors3.azurewebsites.net'`からのフェッチへのアクセスが CORS ポリシーによってブロックされました。要求されたリソースに ' アクセス制御許可-発信元 ' ヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="c3103-376">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="c3103-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="c3103-377">CORS が有効なエンドポイントは、 [curl](https://curl.haxx.se/)、 [Fiddler](https://www.telerik.com/fiddler)、 [Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="c3103-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="c3103-378">ツールを使用する場合、 `Origin`ヘッダーによって指定された要求の配信元は、要求を受信しているホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="c3103-379">ヘッダーの値に基づいて要求が*クロスオリジン*でない場合は、次のようになります。 `Origin`</span><span class="sxs-lookup"><span data-stu-id="c3103-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="c3103-380">CORS ミドルウェアが要求を処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c3103-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="c3103-381">CORS ヘッダーが応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="c3103-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="c3103-382">次のコマンドは`curl` 、を使用して、情報を含む OPTIONS 要求を発行します。</span><span class="sxs-lookup"><span data-stu-id="c3103-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="c3103-383">エンドポイントルーティングを使用した CORS のテスト [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="c3103-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="c3103-384">現在、を使用して、エンドポイント`RequireCors`ごとに CORS を有効にすることは、[自動プレフライト要求](#apf)をサポートして***いません***。</span><span class="sxs-lookup"><span data-stu-id="c3103-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="c3103-385">[エンドポイントルーティングを使用して CORS を有効に](#ecors)する次のコードについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c3103-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="c3103-386">テスト用`TodoItems1Controller`のエンドポイントは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3103-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="c3103-387">配置された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[テストページ](https://cors1.azurewebsites.net/test?number=1)から、前のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="c3103-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="c3103-388">エンドポイントが`[EnableCors]`プレフライト要求に応答して応答するため、 **Delete [enablecors** ] ボタンは正常に**終了します**。</span><span class="sxs-lookup"><span data-stu-id="c3103-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="c3103-389">他のエンドポイントは失敗します。</span><span class="sxs-lookup"><span data-stu-id="c3103-389">The other endpoints fails.</span></span> <span data-ttu-id="c3103-390">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)によってが送信されるため、 **GET**ボタンは失敗します。</span><span class="sxs-lookup"><span data-stu-id="c3103-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="c3103-391">次`TodoItems2Controller`の例は同様のエンドポイントを提供しますが、オプションの要求に応答するための明示的なコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3103-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="c3103-392">配置されたサンプルの[テストページ](https://cors1.azurewebsites.net/test?number=2)から、前のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="c3103-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="c3103-393">[**コントローラー** ] ドロップダウンリストで、[**プレフライト**] を選択し、[**コントローラーの設定**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c3103-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="c3103-394">`TodoItems2Controller`エンドポイントに対するすべての CORS 呼び出しが成功します。</span><span class="sxs-lookup"><span data-stu-id="c3103-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3103-395">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c3103-395">Additional resources</span></span>

* [<span data-ttu-id="c3103-396">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="c3103-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="c3103-397">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="c3103-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c3103-398">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c3103-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c3103-399">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="c3103-400">ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="c3103-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="c3103-401">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="c3103-402">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="c3103-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c3103-403">場合によっては、他のサイトがアプリに対してクロスオリジン要求を行うことを許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="c3103-404">詳細については、「 [MOZILLA CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="c3103-405">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="c3103-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="c3103-406">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="c3103-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="c3103-407">は、CORS 緩和され security のセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="c3103-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="c3103-408">CORS を許可すると、API の安全性が向上しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="c3103-409">詳細については、「 [CORS のしくみ](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="c3103-410">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="c3103-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="c3103-411">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="c3103-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="c3103-412">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c3103-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="c3103-413">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="c3103-413">Same origin</span></span>

<span data-ttu-id="c3103-414">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="c3103-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="c3103-415">この2つの Url のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="c3103-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="c3103-416">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="c3103-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="c3103-417">`https://example.net`&ndash;異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="c3103-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="c3103-418">`https://www.example.com/foo.html`&ndash;異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="c3103-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="c3103-419">`http://example.com/foo.html`&ndash;異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="c3103-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="c3103-420">`https://example.com:9000/foo.html`&ndash;別のポート</span><span class="sxs-lookup"><span data-stu-id="c3103-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="c3103-421">Internet Explorer は、オリジンを比較するときにポートを考慮しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="c3103-422">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="c3103-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="c3103-423">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="c3103-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="c3103-424">次のコードでは、指定したオリジンのアプリ全体に対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="c3103-425">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c3103-425">The preceding code:</span></span>

* <span data-ttu-id="c3103-426">ポリシー名を "\_myallow固有のオリジン" に設定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="c3103-427">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="c3103-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="c3103-428">CORS を<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>有効にする拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="c3103-429"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="c3103-430">ラムダはオブジェクトを<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c3103-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="c3103-431">などの[構成オプション](#cors-policy-options)に`WithOrigins`ついては、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="c3103-432">メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しによって、アプリのサービスコンテナーに CORS サービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="c3103-433">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="c3103-434">メソッド<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>は、次のコードに示すようにメソッドを連結できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="c3103-435">注: URL の末尾にスラッシュ (`/`) を含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="c3103-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="c3103-436">URL がで`/`終了した場合、比較`false`はを返し、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="c3103-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="c3103-437">次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="c3103-438">メモ: `UseCors`の前に`UseMvc`を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="c3103-439">ページ/コントローラー/アクションレベルで CORS ポリシーを適用するには[、「ページ、コントローラー、およびアクションメソッドRazorで cors を有効](#ecors)にする」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="c3103-440">前のコードのようなコードをテストする方法については、「[テスト CORS](#test) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="c3103-441">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="c3103-441">Enable CORS with attributes</span></span>

<span data-ttu-id="c3103-442">[ &lbrack;Enablecors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用するための代替手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="c3103-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="c3103-443">属性`[EnableCors]`を指定すると、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS が有効になります。</span><span class="sxs-lookup"><span data-stu-id="c3103-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="c3103-444">を`[EnableCors]`使用して、既定の`[EnableCors("{Policy String}")]`ポリシーを指定し、ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="c3103-445">属性`[EnableCors]`は次のものに適用できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-445">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="c3103-446">改`PageModel`</span><span class="sxs-lookup"><span data-stu-id="c3103-446"> Page `PageModel`</span></span>
* <span data-ttu-id="c3103-447">コントローラー</span><span class="sxs-lookup"><span data-stu-id="c3103-447">Controller</span></span>
* <span data-ttu-id="c3103-448">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="c3103-448">Controller action method</span></span>

<span data-ttu-id="c3103-449">属性を使用して、 `[EnableCors]`コントローラー/ページモデル/アクションに異なるポリシーを適用できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="c3103-450">`[EnableCors]`属性がコントローラー/ページモデル/アクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、***両方***のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="c3103-451">ポリシーを組み合わせることはお勧めし***ません***。</span><span class="sxs-lookup"><span data-stu-id="c3103-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="c3103-452">`[EnableCors]`属性またはミドルウェアを使用します。両方を使用することはでき**ません**。</span><span class="sxs-lookup"><span data-stu-id="c3103-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="c3103-453">を使用`[EnableCors]`する場合は、既定のポリシー**を定義しないでください**。</span><span class="sxs-lookup"><span data-stu-id="c3103-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="c3103-454">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="c3103-455">次のコードでは、CORS の既定のポリシーと`"AnotherPolicy"`という名前のポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3103-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="c3103-456">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="c3103-456">Disable CORS</span></span>

<span data-ttu-id="c3103-457">[ &lbrack;Disablecors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラー/ページモデル/アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="c3103-458">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="c3103-458">CORS policy options</span></span>

<span data-ttu-id="c3103-459">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="c3103-460">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="c3103-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="c3103-461">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="c3103-462">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="c3103-463">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="c3103-464">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="c3103-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="c3103-465">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="c3103-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>はで`Startup.ConfigureServices`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c3103-467">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="c3103-468">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="c3103-468">Set the allowed origins</span></span>

<span data-ttu-id="c3103-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;任意のスキーム (`http`または`https`) を持つすべてのオリジンからの CORS 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="c3103-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="c3103-470">`AllowAnyOrigin`*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="c3103-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="c3103-471">と`AllowAnyOrigin` `AllowCredentials`を指定すると、安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="c3103-472">セキュリティで保護されたアプリの場合は、クライアントがサーバーリソースへのアクセスを承認する必要がある場合は、オリジンの正確な一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="c3103-473">`AllowAnyOrigin`プレフライト要求とヘッダー `Access-Control-Allow-Origin`に影響します。</span><span class="sxs-lookup"><span data-stu-id="c3103-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="c3103-474">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c3103-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;ポリシーの<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>プロパティを、オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインと一致させることができるようにする関数に設定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="c3103-476">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="c3103-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="c3103-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="c3103-478">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="c3103-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="c3103-479">プレフライト要求とヘッダー `Access-Control-Allow-Methods`に影響します。</span><span class="sxs-lookup"><span data-stu-id="c3103-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="c3103-480">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="c3103-481">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-481">Set the allowed request headers</span></span>

<span data-ttu-id="c3103-482">CORS 要求で特定のヘッダーを送信できるようにするには、 *author 要求ヘッダー*と呼ばれるを呼び出し<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 、許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="c3103-483">すべての作成者要求ヘッダーを許可<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>するには、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="c3103-484">この設定は、プレフライト要求`Access-Control-Request-Headers`とヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="c3103-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="c3103-485">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c3103-486">CORS ミドルウェアでは、CorsPolicy で構成`Access-Control-Request-Headers`されている値に関係なく、常にの4つのヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="c3103-487">このヘッダーの一覧には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="c3103-488">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c3103-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="c3103-489">は常にホワイトリストに登録されているため`Content-Language` 、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求に正常に応答します。</span><span class="sxs-lookup"><span data-stu-id="c3103-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="c3103-490">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-490">Set the exposed response headers</span></span>

<span data-ttu-id="c3103-491">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="c3103-492">詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="c3103-493">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3103-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="c3103-494">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="c3103-495">アプリで他のヘッダーを使用できるように<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>するには、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="c3103-496">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="c3103-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="c3103-497">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="c3103-498">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="c3103-499">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="c3103-500">クロスオリジン要求で資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials`に`true`設定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="c3103-501">直接`XMLHttpRequest`の使用:</span><span class="sxs-lookup"><span data-stu-id="c3103-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="c3103-502">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="c3103-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="c3103-503">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="c3103-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="c3103-504">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-504">The server must allow the credentials.</span></span> <span data-ttu-id="c3103-505">クロスオリジンの資格情報を許可する<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>には、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="c3103-506">HTTP 応答には、 `Access-Control-Allow-Credentials`サーバーがクロスオリジン要求に対して資格情報を許可することをブラウザーに示すヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3103-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="c3103-507">ブラウザーが資格情報を送信しても、応答に`Access-Control-Allow-Credentials`有効なヘッダーが含まれていない場合、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="c3103-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="c3103-508">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="c3103-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="c3103-509">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="c3103-510">また、CORS 仕様では、 `"*"` `Access-Control-Allow-Credentials`ヘッダーが存在する場合、[オリジン] を [(すべてのオリジン)] に設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="c3103-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="c3103-511">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="c3103-511">Preflight requests</span></span>

<span data-ttu-id="c3103-512">一部の CORS 要求については、ブラウザーは実際の要求を行う前に追加の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="c3103-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="c3103-513">この要求は*プレフライト要求*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="c3103-514">次の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="c3103-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="c3103-515">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="c3103-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="c3103-516">`Accept`アプリでは、 `Accept-Language`、、 `Content-Language`、 `Content-Type`、または以外の要求`Last-Event-ID`ヘッダーは設定されません。</span><span class="sxs-lookup"><span data-stu-id="c3103-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="c3103-517">ヘッダー `Content-Type`には、設定されている場合、次のいずれかの値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="c3103-518">クライアント要求に対して設定された要求ヘッダーに適用される規則は、 `setRequestHeader` `XMLHttpRequest`オブジェクトに対してを呼び出すことによって、アプリが設定するヘッダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="c3103-519">CORS 仕様は、これらのヘッダー*作成者要求ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="c3103-520">このルールは`User-Agent`、ブラウザーが設定できるヘッダー (、 `Host`、など) には`Content-Length`適用されません。</span><span class="sxs-lookup"><span data-stu-id="c3103-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="c3103-521">プレフライト要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="c3103-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="c3103-522">事前フライト要求では、HTTP OPTIONS メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="c3103-523">次の2つの特殊なヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3103-523">It includes two special headers:</span></span>

* <span data-ttu-id="c3103-524">`Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="c3103-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="c3103-525">`Access-Control-Request-Headers`: アプリが実際の要求に対して設定する要求ヘッダーのリスト。</span><span class="sxs-lookup"><span data-stu-id="c3103-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="c3103-526">前述のように、これにはブラウザーが設定するヘッダー (など`User-Agent`) は含まれません。</span><span class="sxs-lookup"><span data-stu-id="c3103-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="c3103-527">CORS が適切なポリシーで有効になっている場合、ASP.NET Core は通常、CORS プレフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="c3103-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="c3103-528">[プレフライト要求の場合は、[HttpOptions] 属性を](#pro)参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3103-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="c3103-529">CORS のプレフライト要求には`Access-Control-Request-Headers` 、実際の要求と共に送信されるヘッダーをサーバーに示すヘッダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c3103-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="c3103-530">特定のヘッダーを許可する<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>には、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="c3103-531">すべての作成者要求ヘッダーを許可<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>するには、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="c3103-532">ブラウザーの設定`Access-Control-Request-Headers`方法が完全に一致しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="c3103-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="c3103-533">ヘッダーを`"*"`以外の任意の値 (またはを<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>使用) に設定する場合は`Accept`、 `Content-Type`少なくと`Origin`も、、、、およびサポートするカスタムヘッダーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="c3103-534">プレフライト要求に対する応答の例を次に示します (サーバーが要求を許可していることを前提としています)。</span><span class="sxs-lookup"><span data-stu-id="c3103-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="c3103-535">応答には、 `Access-Control-Allow-Methods`許可されているメソッドと、必要`Access-Control-Allow-Headers`に応じてヘッダーを一覧表示するヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3103-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="c3103-536">プレフライト要求が成功した場合、ブラウザーは実際の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="c3103-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="c3103-537">プレフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="c3103-538">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="c3103-539">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="c3103-539">Set the preflight expiration time</span></span>

<span data-ttu-id="c3103-540">ヘッダー `Access-Control-Max-Age`は、プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3103-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="c3103-541">このヘッダーを設定するに<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>は、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3103-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="c3103-542">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="c3103-542">How CORS works</span></span>

<span data-ttu-id="c3103-543">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="c3103-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="c3103-544">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="c3103-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="c3103-545">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="c3103-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="c3103-546">たとえば、悪意のあるアクターがを使用してサイトに対して[クロスサイトスクリプティング (XSS) を防止](xref:security/cross-site-scripting)し、CORS が有効になっているサイトに対してクロスサイト要求を実行して情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="c3103-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="c3103-547">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="c3103-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="c3103-548">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="c3103-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="c3103-549">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="c3103-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="c3103-550">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="c3103-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="c3103-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="c3103-552">Postman</span><span class="sxs-lookup"><span data-stu-id="c3103-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="c3103-553">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="c3103-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="c3103-554">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="c3103-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="c3103-555">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="c3103-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="c3103-556">(CORS を使用しない) ブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="c3103-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="c3103-557">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="c3103-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="c3103-558">JSONP は XHR を使用しないの`<script>`で、タグを使用して応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="c3103-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="c3103-559">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="c3103-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="c3103-560">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="c3103-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="c3103-561">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="c3103-562">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="c3103-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="c3103-563">次に、クロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="c3103-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="c3103-564">ヘッダー `Origin`は、要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="c3103-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="c3103-565">`Origin`ヘッダーは必須であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="c3103-566">サーバーが要求を許可している場合は`Access-Control-Allow-Origin` 、応答でヘッダーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="c3103-567">このヘッダーの値は、要求の`Origin`ヘッダーと一致するか、またはワイルド`"*"`カード値です。つまり、すべての配信元が許可されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="c3103-568">応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="c3103-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="c3103-569">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="c3103-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="c3103-570">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="c3103-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="c3103-571">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="c3103-571">Test CORS</span></span>

<span data-ttu-id="c3103-572">CORS をテストするには:</span><span class="sxs-lookup"><span data-stu-id="c3103-572">To test CORS:</span></span>

1. <span data-ttu-id="c3103-573">[API プロジェクトを作成](xref:tutorials/first-web-api)します。</span><span class="sxs-lookup"><span data-stu-id="c3103-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="c3103-574">または、[サンプルをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。</span><span class="sxs-lookup"><span data-stu-id="c3103-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="c3103-575">このドキュメントのいずれかの方法を使用して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="c3103-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="c3103-576">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c3103-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="c3103-577">`WithOrigins("https://localhost:<port>");`サンプルアプリのテストにのみ使用してください[サンプルコードをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)します。</span><span class="sxs-lookup"><span data-stu-id="c3103-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="c3103-578">Web アプリプロジェクトを作成しRazorます (ページまたは MVC)。</span><span class="sxs-lookup"><span data-stu-id="c3103-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="c3103-579">このサンプルでRazorは、ページを使用します。</span><span class="sxs-lookup"><span data-stu-id="c3103-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="c3103-580">API プロジェクトと同じソリューションに web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="c3103-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="c3103-581">次の強調表示されたコードを*インデックスの cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c3103-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="c3103-582">前のコードで、を`url: 'https://<web app>.azurewebsites.net/api/values/1',`デプロイされたアプリの URL に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c3103-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="c3103-583">API プロジェクトをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="c3103-583">Deploy the API project.</span></span> <span data-ttu-id="c3103-584">たとえば、 [Azure にデプロイ](xref:host-and-deploy/azure-apps/index)します。</span><span class="sxs-lookup"><span data-stu-id="c3103-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="c3103-585">デスクトップからRazorページまたは MVC アプリを実行し、[**テスト**] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c3103-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="c3103-586">F12 ツールを使用して、エラーメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="c3103-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="c3103-587">から`WithOrigins` localhost の配信元を削除し、アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="c3103-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="c3103-588">または、別のポートを使用してクライアントアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c3103-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="c3103-589">たとえば、Visual Studio からを実行します。</span><span class="sxs-lookup"><span data-stu-id="c3103-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="c3103-590">クライアントアプリでテストします。</span><span class="sxs-lookup"><span data-stu-id="c3103-590">Test with the client app.</span></span> <span data-ttu-id="c3103-591">CORS エラーはエラーを返しますが、エラーメッセージは JavaScript では使用できません。</span><span class="sxs-lookup"><span data-stu-id="c3103-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="c3103-592">F12 ツールの [コンソール] タブを使用して、エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="c3103-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="c3103-593">ブラウザーによっては、次のようなエラー (F12 ツールコンソール) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3103-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="c3103-594">Microsoft Edge を使用する:</span><span class="sxs-lookup"><span data-stu-id="c3103-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="c3103-595">**SEC7120: [CORS] オリジン`https://localhost:44375`は、次の`https://localhost:44375`場所にあるクロスオリジンリソースのアクセス制御-許可-オリジン応答ヘッダーで見つかりませんでした`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="c3103-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="c3103-596">Chrome を使用する:</span><span class="sxs-lookup"><span data-stu-id="c3103-596">Using Chrome:</span></span>

     <span data-ttu-id="c3103-597">**オリジン`https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375`からの XMLHttpRequest へのアクセスが CORS ポリシーによってブロックされています。要求されたリソースに ' アクセス制御許可-発信元 ' ヘッダーが存在しません。**</span><span class="sxs-lookup"><span data-stu-id="c3103-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="c3103-598">CORS が有効なエンドポイントは、 [Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="c3103-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="c3103-599">ツールを使用する場合、 `Origin`ヘッダーによって指定された要求の配信元は、要求を受信しているホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="c3103-600">ヘッダーの値に基づいて要求が*クロスオリジン*でない場合は、次のようになります。 `Origin`</span><span class="sxs-lookup"><span data-stu-id="c3103-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="c3103-601">CORS ミドルウェアが要求を処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c3103-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="c3103-602">CORS ヘッダーが応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="c3103-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="c3103-603">IIS での CORS</span><span class="sxs-lookup"><span data-stu-id="c3103-603">CORS in IIS</span></span>

<span data-ttu-id="c3103-604">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合、CORS は Windows 認証の前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="c3103-605">このシナリオをサポートするには、アプリ用に[IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールして構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3103-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3103-606">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c3103-606">Additional resources</span></span>

* [<span data-ttu-id="c3103-607">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="c3103-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="c3103-608">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="c3103-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
