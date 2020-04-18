---
title: ASP.NETコアでのクロスオリジンリクエスト(CORS)の有効化
author: rick-anderson
description: ASP.NET Core アプリでクロスオリジン要求を許可または拒否するための標準として CORS を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642703"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="a6a04-103">ASP.NETコアでのクロスオリジンリクエスト(CORS)の有効化</span><span class="sxs-lookup"><span data-stu-id="a6a04-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6a04-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="a6a04-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="a6a04-105">この記事では、ASP.NET コア アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="a6a04-106">ブラウザのセキュリティにより、Web ページが Web ページを提供したドメインとは異なるドメインに対して要求を行えないようにします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a6a04-107">この制限は、*同一生成元ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="a6a04-108">同一生成元ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取るのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a6a04-109">場合によっては、他のサイトでアプリに対してクロスオリジン要求を行うことを許可する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="a6a04-110">詳細については、 [Mozilla CORS の記事](https://developer.mozilla.org/docs/Web/HTTP/CORS)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="a6a04-111">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="a6a04-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="a6a04-112">サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="a6a04-113">セキュリティ機能**ではない**、CORS はセキュリティを緩和します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="a6a04-114">API は CORS を許可することで安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="a6a04-115">詳細については[、「CORS の仕組み](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="a6a04-116">サーバーが、一部のクロスオリジン要求を許可し、その他の要求を拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="a6a04-117">[JSONP](/dotnet/framework/wcf/samples/jsonp)などの以前の手法よりも安全で柔軟性があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="a6a04-118">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a6a04-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="a6a04-119">同じ起源</span><span class="sxs-lookup"><span data-stu-id="a6a04-119">Same origin</span></span>

<span data-ttu-id="a6a04-120">2 つの URL が同じスキーム、ホスト、およびポートを持つ場合は、同じオリジンを持ちます ([RFC 6454](https://tools.ietf.org/html/rfc6454))。</span><span class="sxs-lookup"><span data-stu-id="a6a04-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="a6a04-121">これらの 2 つの URL のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="a6a04-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="a6a04-122">これらの URL のオリジンは、前の 2 つの URL とは異なります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="a6a04-123">`https://example.net`&ndash;異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="a6a04-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="a6a04-124">`https://www.example.com/foo.html`&ndash;異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="a6a04-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="a6a04-125">`http://example.com/foo.html`&ndash;異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="a6a04-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="a6a04-126">`https://example.com:9000/foo.html`&ndash;異なるポート</span><span class="sxs-lookup"><span data-stu-id="a6a04-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="a6a04-127">CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="a6a04-127">Enable CORS</span></span>

<span data-ttu-id="a6a04-128">CORS を有効にするには、次の 3 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="a6a04-129">ミドルウェアで[名前付きポリシー](#np)または[デフォルトポリシー](#dp)を使用する 。</span><span class="sxs-lookup"><span data-stu-id="a6a04-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="a6a04-130">[エンドポイント ルーティング](#ecors)を使用する :</span><span class="sxs-lookup"><span data-stu-id="a6a04-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="a6a04-131">[[EnableCors] 属性を使用します](#attr)。</span><span class="sxs-lookup"><span data-stu-id="a6a04-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="a6a04-132">名前付きポリシーで[[EnableCors]](#attr)属性を使用すると、CORS をサポートするエンドポイントを制限する際に、最も優れた制御が提供されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="a6a04-133">各方法については、以下のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="a6a04-134">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="a6a04-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="a6a04-135">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="a6a04-136">次のコードでは、指定したオリジンを持つすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="a6a04-137">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-137">The preceding code:</span></span>

* <span data-ttu-id="a6a04-138">ポリシー名を に`_myAllowSpecificOrigins`設定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="a6a04-139">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="a6a04-140">拡張メソッド<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>を呼び出し`_myAllowSpecificOrigins`、CORS ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="a6a04-141">`UseCors`CORS ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="a6a04-142">の`UseCors`呼び出しは、`UseRouting`の後`UseAuthorization`に置く必要がありますが、 の前に置く必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="a6a04-143">詳細については、[ミドルウェアの順序](xref:fundamentals/middleware/index#middleware-order)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="a6a04-144"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="a6a04-145">ラムダはオブジェクト<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="a6a04-146">などの`WithOrigins`[構成オプション](#cors-policy-options)については、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="a6a04-147">すべてのコントローラー`_myAllowSpecificOrigins`エンドポイントの CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="a6a04-148">特定[のエンドポイント](#ecors)に CORS ポリシーを適用するには、エンドポイントルーティングを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="a6a04-149">エンドポイント ルーティングでは、CORS ミドルウェアが 呼び出しと`UseRouting`の`UseEndpoints`間で実行されるように構成する***必要があります***。</span><span class="sxs-lookup"><span data-stu-id="a6a04-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="a6a04-150">上記のコードと同様のコードのテスト手順については、「[テスト CORS」](#testc)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="a6a04-151">メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しは、アプリのサービス コンテナーに CORS サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="a6a04-152">詳細については、このドキュメントの[CORS ポリシー オプション](#cpo)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="a6a04-153">次<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>のコードに示すように、メソッドは連鎖できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="a6a04-154">注意 : 指定された**not**URL に末尾の`/`スラッシュ ( ) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="a6a04-155">URL が で`/`終わる場合、比較`false`は返され、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="a6a04-156">デフォルトポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="a6a04-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="a6a04-157">次の強調表示されたコードは、既定の CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="a6a04-158">上記のコードでは、すべてのコントローラ エンドポイントにデフォルトの CORS ポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="a6a04-159">エンドポイント ルーティングで CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="a6a04-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="a6a04-160">現在の使用を使用して`RequireCors`エンドポイントごとに CORS を有効にしても、[自動プリフライト要求](#apf)はサポート***されません***。</span><span class="sxs-lookup"><span data-stu-id="a6a04-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="a6a04-161">詳細については、この[GitHub の問題](https://github.com/dotnet/aspnetcore/issues/20709)と[エンドポイント ルーティングを使用した CORS のテストと [HttpOptions]](#tcer)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="a6a04-162">エンドポイント ルーティングを使用すると、次の拡張メソッドを使用してエンドポイントごとに<xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*>CORS を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="a6a04-163">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-163">In the preceding code:</span></span>

* <span data-ttu-id="a6a04-164">`app.UseCors`を使用して、CORS ミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="a6a04-165">デフォルトポリシーが設定されていないため、`app.UseCors()`単独では CORS を有効にしません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="a6a04-166">および`/echo`コントローラ エンドポイントは、指定されたポリシーを使用して、クロス オリジン要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="a6a04-167">`/echo2`および Razor ページ エンドポイントでは、既定のポリシーが指定されていないため、クロス オリジン要求は許可***されません***。</span><span class="sxs-lookup"><span data-stu-id="a6a04-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="a6a04-168">[[DisableCors]](#dc)属性は、エンドポイント ルーティングによって有効になっている CORS を`RequireCors`無効***にしません***。</span><span class="sxs-lookup"><span data-stu-id="a6a04-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="a6a04-169">上記のようなコードのテスト手順については、「[エンドポイントルーティングを使用した CORS のテスト」および「HttpOptions」](#tcer)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="a6a04-170">属性を使用した CORS の有効化</span><span class="sxs-lookup"><span data-stu-id="a6a04-170">Enable CORS with attributes</span></span>

<span data-ttu-id="a6a04-171">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性を使用して CORS を有効にし、CORS を必要とするエンドポイントにのみ名前付きポリシーを適用すると、最高の制御が提供されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="a6a04-172">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="a6a04-173">この`[EnableCors]`属性は、すべてのエンドポイントではなく、選択したエンドポイントに CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="a6a04-174">`[EnableCors]`は、既定のポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="a6a04-175">`[EnableCors("{Policy String}")]`は、名前付きポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="a6a04-176">属性`[EnableCors]`は、次の場合に適用できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="a6a04-177">カミソリページ`PageModel`</span><span class="sxs-lookup"><span data-stu-id="a6a04-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="a6a04-178">コントローラー</span><span class="sxs-lookup"><span data-stu-id="a6a04-178">Controller</span></span>
* <span data-ttu-id="a6a04-179">コントローラアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="a6a04-179">Controller action method</span></span>

<span data-ttu-id="a6a04-180">属性を使用して、コントローラ、ページ モデル、アクション メソッドに異なる`[EnableCors]`ポリシーを適用できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="a6a04-181">属性が`[EnableCors]`コントローラー、ページ・モデル、またはアクション・メソッドに適用され、CORS がミドルウェアで有効になっている場合、***両方の***ポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="a6a04-182">***ポリシーの組み合わせに対しては、お勧めします。***`[EnableCors]`***同じアプリで属性またはミドルウェアを使用する必要はありません。***</span><span class="sxs-lookup"><span data-stu-id="a6a04-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="a6a04-183">次のコードでは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="a6a04-184">次のコードでは、2 つの CORS ポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="a6a04-185">CORS 要求を制限する最も優れた制御を行う場合:</span><span class="sxs-lookup"><span data-stu-id="a6a04-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="a6a04-186">名前`[EnableCors("MyPolicy")]`付きポリシーで使用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="a6a04-187">既定のポリシーを定義しないでください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-187">Don't define a default policy.</span></span>
* <span data-ttu-id="a6a04-188">[エンドポイント ルーティング](#ecors)を使用しない。</span><span class="sxs-lookup"><span data-stu-id="a6a04-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="a6a04-189">次のセクションのコードは、上記の一覧を満たしています。</span><span class="sxs-lookup"><span data-stu-id="a6a04-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="a6a04-190">上記のコードと同様のコードのテスト手順については、「[テスト CORS」](#testc)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="a6a04-191">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="a6a04-191">Disable CORS</span></span>

<span data-ttu-id="a6a04-192">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は[、エンドポイント ルーティング](#ecors)によって有効にされた CORS を無効***にしません***。</span><span class="sxs-lookup"><span data-stu-id="a6a04-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="a6a04-193">次のコードは、CORS`"MyPolicy"`ポリシーを定義します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="a6a04-194">次のコードは、アクションの CORS を無効にします`GetValues2`。</span><span class="sxs-lookup"><span data-stu-id="a6a04-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="a6a04-195">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-195">The preceding code:</span></span>

* <span data-ttu-id="a6a04-196">[エンドポイント ルーティング](#ecors)で CORS を有効にしません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="a6a04-197">既定の[CORS ポリシー](#dp)を定義しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="a6a04-198">[[EnableCors("MyPolicy")]を](#attr)使用して`"MyPolicy"`、コントローラのCORSポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="a6a04-199">メソッドの CORS`GetValues2`を無効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="a6a04-200">上記のコードのテスト手順については、「[テスト CORS」](#testc)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="a6a04-201">CORS ポリシー オプション</span><span class="sxs-lookup"><span data-stu-id="a6a04-201">CORS policy options</span></span>

<span data-ttu-id="a6a04-202">このセクションでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="a6a04-203">許可された原点を設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="a6a04-204">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="a6a04-205">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="a6a04-206">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="a6a04-207">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="a6a04-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="a6a04-208">プリフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="a6a04-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>が で`Startup.ConfigureServices`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a6a04-210">いくつかのオプションについては、[最初に「CORS の仕組み](#how-cors)」のセクションを読んでも役に立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="a6a04-211">許可された原点を設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-211">Set the allowed origins</span></span>

<span data-ttu-id="a6a04-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;任意のスキーム ( または`http``https`) を持つすべてのオリジンからの CORS 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="a6a04-213">`AllowAnyOrigin`*どのウェブサイトも*アプリにクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a6a04-214">指定され`AllowAnyOrigin`、`AllowCredentials`安全でない構成であり、クロスサイトリクエストフォージェリにつながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="a6a04-215">アプリが両方のメソッドで構成されている場合、CORS サービスは無効な CORS 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="a6a04-216">`AllowAnyOrigin`はプリフライト要求とヘッダー`Access-Control-Allow-Origin`に影響します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="a6a04-217">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a6a04-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;ポリシーの<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>プロパティを、オリジンが許可されているかどうかを評価するときに、設定されたワイルドカード ドメインとオリジンが一致するように設定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="a6a04-219">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="a6a04-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="a6a04-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="a6a04-221">任意の HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="a6a04-222">プリフライト要求とヘッダーに`Access-Control-Allow-Methods`影響します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="a6a04-223">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="a6a04-224">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-224">Set the allowed request headers</span></span>

<span data-ttu-id="a6a04-225">特定のヘッダーを CORS 要求で送信できるようにするには[、author 要求ヘッダー](https://xhr.spec.whatwg.org/#request)と呼<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>びます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="a6a04-226">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="a6a04-227">`AllowAnyHeader`は、プリフライト要求および[アクセス制御要求ヘッダーヘッダーに影響します](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)。</span><span class="sxs-lookup"><span data-stu-id="a6a04-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="a6a04-228">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a6a04-229">CORS ミドルウェア ポリシーは、 で`WithHeaders`指定された特定のヘッダー`Access-Control-Request-Headers`と一致する場合にのみ可能です。 `WithHeaders`</span><span class="sxs-lookup"><span data-stu-id="a6a04-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="a6a04-230">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="a6a04-231">CORS ミドルウェアは、`Content-Language`[に一](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)覧されていないため、次の要求ヘッダーを使用してプレフライト要求を`WithHeaders`拒否します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="a6a04-232">アプリは*200 OK*応答を返しますが、CORS ヘッダーを返しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="a6a04-233">したがって、ブラウザーは、クロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="a6a04-234">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-234">Set the exposed response headers</span></span>

<span data-ttu-id="a6a04-235">既定では、ブラウザーは、すべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="a6a04-236">詳細については、「 [W3C クロス オリジン リソース共有 (用語集): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="a6a04-237">デフォルトで使用可能な応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a6a04-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="a6a04-238">CORS 仕様では、これらのヘッダーの*単純な応答ヘッダーを*呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="a6a04-239">他のヘッダーをアプリで使用できるようにするには、次の<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="a6a04-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="a6a04-240">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="a6a04-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="a6a04-241">資格情報には、CORS 要求での特別な処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="a6a04-242">既定では、ブラウザーはクロスオリジン要求を含む資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="a6a04-243">資格情報には、Cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="a6a04-244">クロスオリジン要求を使用して資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials``true`に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="a6a04-245">直接`XMLHttpRequest`使用:</span><span class="sxs-lookup"><span data-stu-id="a6a04-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="a6a04-246">jQuery を使用する:</span><span class="sxs-lookup"><span data-stu-id="a6a04-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="a6a04-247">フェッチ[API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用 :</span><span class="sxs-lookup"><span data-stu-id="a6a04-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="a6a04-248">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-248">The server must allow the credentials.</span></span> <span data-ttu-id="a6a04-249">クロスオリジンの資格情報を許可するには、次<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="a6a04-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="a6a04-250">HTTP 応答には`Access-Control-Allow-Credentials`ヘッダーが含まれており、サーバーがクロスオリジン要求の資格情報を許可することをブラウザーに伝えます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="a6a04-251">ブラウザーが資格情報を送信しても、応答に有効な`Access-Control-Allow-Credentials`ヘッダーが含まれていない場合、ブラウザーはアプリに対する応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="a6a04-252">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="a6a04-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="a6a04-253">別のドメインの Web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="a6a04-254">CORS 仕様では、ヘッダーが存在する場合`"*"`、(すべての起点) に原点`Access-Control-Allow-Credentials`を設定することは無効であるとも述べています。</span><span class="sxs-lookup"><span data-stu-id="a6a04-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="a6a04-255">プリフライトリクエスト</span><span class="sxs-lookup"><span data-stu-id="a6a04-255">Preflight requests</span></span>

<span data-ttu-id="a6a04-256">一部の CORS 要求では、ブラウザーは実際の要求を行う前に追加[の OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="a6a04-257">この要求は[、プリフライト要求](https://developer.mozilla.org/docs/Glossary/Preflight_request)と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="a6a04-258">次***の条件がすべて***満たしている場合、ブラウザはプリフライトリクエストをスキップできます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="a6a04-259">要求メソッドは、GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="a6a04-260">アプリ`Accept`は、 、 、 `Accept-Language`、 、、`Content-Language``Content-Type`または`Last-Event-ID`以外の要求ヘッダーを設定しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="a6a04-261">ヘッダー`Content-Type`が設定されている場合、次のいずれかの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="a6a04-262">クライアント要求に設定された要求ヘッダーのルールは、アプリケーションがオブジェクトを呼び出`setRequestHeader`すことによって設定するヘッダーに`XMLHttpRequest`適用されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="a6a04-263">CORS 仕様では、これらのヘッダー[の作成者要求ヘッダーを](https://www.w3.org/TR/cors/#author-request-headers)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="a6a04-264">ルールは、 、 、 など、`User-Agent``Host`ブラウザで設定できるヘッダーには適用されません。 `Content-Length`</span><span class="sxs-lookup"><span data-stu-id="a6a04-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="a6a04-265">次に示す応答は、このドキュメントの[「テスト CORS」](#testc)セクションの **[Put test]** ボタンから行われたプリフライト要求に似た応答です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="a6a04-266">プリフライト要求では[、HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="a6a04-267">次のヘッダーが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-267">It may include the following headers:</span></span>

* <span data-ttu-id="a6a04-268">[アクセス制御要求メソッド](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="a6a04-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="a6a04-269">[アクセスコントロール要求ヘッダー](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): アプリが実際の要求に設定する要求ヘッダーのリスト。</span><span class="sxs-lookup"><span data-stu-id="a6a04-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="a6a04-270">前述のように、ブラウザーが設定するヘッダーは含みません`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="a6a04-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="a6a04-271">アクセス制御許可メソッド</span><span class="sxs-lookup"><span data-stu-id="a6a04-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="a6a04-272">プリフライト要求が拒否された場合、アプリは応答を`200 OK`返しますが、CORS ヘッダーは設定しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="a6a04-273">したがって、ブラウザーは、クロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="a6a04-274">拒否されたプリフライト要求の例については、このドキュメントの[「CORS](#testc)のテスト」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="a6a04-275">F12 ツールを使用すると、コンソール アプリはブラウザーに応じて、次のいずれかと同様のエラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="a6a04-276">Firefox: クロスオリジンリクエストブロック: 同じオリジンポリシーでは、 で`https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`リモートリソースの読み取りを禁止します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="a6a04-277">(理由: CORS 要求が成功しませんでした)。</span><span class="sxs-lookup"><span data-stu-id="a6a04-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="a6a04-278">詳細情報</span><span class="sxs-lookup"><span data-stu-id="a6a04-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="a6a04-279">クロムベース: ''https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' でのフェッチへのアクセスが CORS ポリシーによってブロックされました: プリフライト要求への応答はアクセス制御チェックに合格しません: 要求されたリソースに 'アクセス制御-許可-Origin' ヘッダーがありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="a6a04-280">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="a6a04-281">特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="a6a04-282">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="a6a04-283">ブラウザは、 の設定`Access-Control-Request-Headers`方法に一貫性がありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="a6a04-284">次のいずれかの場合:</span><span class="sxs-lookup"><span data-stu-id="a6a04-284">If either:</span></span>

* <span data-ttu-id="a6a04-285">ヘッダーは、次以外の値に設定されます。`"*"`</span><span class="sxs-lookup"><span data-stu-id="a6a04-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="a6a04-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>呼び出し:`Accept`少`Content-Type`なくとも`Origin`、 、および を含め、サポートするカスタム ヘッダーを含めます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="a6a04-287">自動プリフライトリクエストコード</span><span class="sxs-lookup"><span data-stu-id="a6a04-287">Automatic preflight request code</span></span>

<span data-ttu-id="a6a04-288">CORS ポリシーが適用されると、次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="a6a04-289">を呼び出`app.UseCors`すことによって`Startup.Configure`グローバルに.</span><span class="sxs-lookup"><span data-stu-id="a6a04-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="a6a04-290">属性を`[EnableCors]`使用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="a6a04-291">ASP.NETコアは、プレフライトの OPTIONS 要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="a6a04-292">現在の使用を使用して`RequireCors`エンドポイントごとに CORS を有効にすることは、自動プリフライト要求をサポート***していません***。</span><span class="sxs-lookup"><span data-stu-id="a6a04-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="a6a04-293">このドキュメントの[「テスト CORS」](#testc)セクションでは、この動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="a6a04-294">プリフライト要求の [Http オプション] 属性</span><span class="sxs-lookup"><span data-stu-id="a6a04-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="a6a04-295">適切なポリシーで CORS が有効になっている場合、ASP.NETコアは通常 CORS プリフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="a6a04-296">シナリオによっては、この場合に該当しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="a6a04-297">たとえば、エンドポイント[ルーティングで CORS](#ecors)を使用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="a6a04-298">次のコードでは、OPTIONS 要求のエンドポイントを作成するために[[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="a6a04-299">上記のコードのテスト手順については、「[エンドポイント ルーティングを使用した CORS のテスト」および「HttpOptions」](#tcer)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="a6a04-300">プリフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-300">Set the preflight expiration time</span></span>

<span data-ttu-id="a6a04-301">ヘッダー`Access-Control-Max-Age`は、プリフライト要求に対する応答をキャッシュできる時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="a6a04-302">このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="a6a04-303">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="a6a04-303">How CORS works</span></span>

<span data-ttu-id="a6a04-304">このセクションでは、HTTP メッセージのレベルで[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求で何が起こるかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="a6a04-305">CORS はセキュリティ機能**ではありません**。</span><span class="sxs-lookup"><span data-stu-id="a6a04-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="a6a04-306">CORS は、サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="a6a04-307">たとえば、悪意のあるアクターがサイトに対して[クロスサイト スクリプティング (XSS) を](xref:security/cross-site-scripting)使用し、CORS 対応サイトに対してクロスサイト要求を実行して情報を盗む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="a6a04-308">API は CORS を許可することで安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="a6a04-309">CORS を強制するのはクライアント (ブラウザー) の管理です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="a6a04-310">サーバーは要求を実行し、応答を返します、それはエラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="a6a04-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="a6a04-311">たとえば、次のツールのどれでもサーバーの応答を表示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="a6a04-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="a6a04-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="a6a04-313">Postman</span><span class="sxs-lookup"><span data-stu-id="a6a04-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="a6a04-314">.NET Http クライアント</span><span class="sxs-lookup"><span data-stu-id="a6a04-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="a6a04-315">アドレス バーに URL を入力して Web ブラウザを表示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="a6a04-316">これは、ブラウザがクロスオリジン[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行することを許可するサーバーの方法です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="a6a04-317">CORS を使用しないブラウザーは、クロスオリジン要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="a6a04-318">CORS より前では[、JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)はこの制限を回避するために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="a6a04-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="a6a04-319">JSONP は XHR を使用せず、`<script>`タグを使用して応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="a6a04-320">スクリプトは、クロスオリジンでロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="a6a04-321">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にするいくつかの新しい HTTP ヘッダーが導入されました。</span><span class="sxs-lookup"><span data-stu-id="a6a04-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="a6a04-322">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="a6a04-323">カスタム JavaScript コードは、CORS を有効にする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="a6a04-324">展開された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[PUT テスト ボタン](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="a6a04-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="a6a04-325">次に示しているのは、[値](https://cors3.azurewebsites.net/)テスト ボタンから に対するクロスオリジン要求`https://cors1.azurewebsites.net/api/values`の例です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="a6a04-326">`Origin`ヘッダー:</span><span class="sxs-lookup"><span data-stu-id="a6a04-326">The `Origin` header:</span></span>

* <span data-ttu-id="a6a04-327">要求を行うサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="a6a04-328">必要であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="a6a04-329">**一般ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="a6a04-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="a6a04-330">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="a6a04-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="a6a04-331">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="a6a04-331">**Request headers**</span></span>

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

<span data-ttu-id="a6a04-332">要求`OPTIONS`では、サーバーは応答ヘッダー**ヘッダーを**`Access-Control-Allow-Origin: {allowed origin}`応答に設定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="a6a04-333">たとえば、デプロイされた[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[[削除 [EnableCors]](https://cors1.azurewebsites.net/test?number=2)ボタン`OPTIONS`要求には、次のヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a6a04-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="a6a04-334">**一般ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="a6a04-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="a6a04-335">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="a6a04-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="a6a04-336">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="a6a04-336">**Request headers**</span></span>

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

<span data-ttu-id="a6a04-337">上記の**応答ヘッダーでは、** サーバーは応答に[アクセス制御-許可-オリジン](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="a6a04-338">この`https://cors1.azurewebsites.net`ヘッダーの値は、要求`Origin`のヘッダーと一致します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="a6a04-339">が<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>呼び出されると`Access-Control-Allow-Origin: *`、ワイルドカード値が返されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="a6a04-340">`AllowAnyOrigin`任意の原点を許可します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="a6a04-341">応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="a6a04-342">具体的には、ブラウザーは要求を拒否します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="a6a04-343">サーバーが成功した応答を返した場合でも、ブラウザーはクライアント アプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="a6a04-344">オプション要求の表示</span><span class="sxs-lookup"><span data-stu-id="a6a04-344">Display OPTIONS requests</span></span>

<span data-ttu-id="a6a04-345">デフォルトでは、Chrome と Edge のブラウザでは、F12 ツールのネットワーク タブに OPTIONS リクエストは表示されません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="a6a04-346">これらのブラウザで OPTIONS 要求を表示するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="a6a04-347">`chrome://flags/#out-of-blink-cors` または `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="a6a04-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="a6a04-348">フラグを無効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-348">disable the flag.</span></span>
* <span data-ttu-id="a6a04-349">[再起動] をタップします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-349">restart.</span></span>

<span data-ttu-id="a6a04-350">デフォルトでは、オプションリクエストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="a6a04-351">IIS の CORS</span><span class="sxs-lookup"><span data-stu-id="a6a04-351">CORS in IIS</span></span>

<span data-ttu-id="a6a04-352">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合は、Windows 認証の前に CORS を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="a6a04-353">このシナリオをサポートするには[、IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールし、アプリ用に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="a6a04-354">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="a6a04-354">Test CORS</span></span>

<span data-ttu-id="a6a04-355">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)には、CORS をテストするためのコードがあります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="a6a04-356">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="a6a04-357">サンプルは、Razor ページが追加された API プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="a6a04-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="a6a04-358">`WithOrigins("https://localhost:<port>");`ダウンロード サンプル[コード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)に似たサンプル アプリのテストにのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="a6a04-359">テストの`ValuesController`エンドポイントを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="a6a04-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)は[、リック.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet パッケージによって提供され、ルート情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="a6a04-361">次のいずれかの方法を使用して、上記のサンプル コードをテストします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="a6a04-362">デプロイされたサンプル アプリを使用[https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="a6a04-363">サンプルをダウンロードする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="a6a04-364">サンプルを実行するには`dotnet run`、既定の`https://localhost:5001`URL を使用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="a6a04-365">の URL に対してポートを 44398 に設定して、Visual Studio からサンプルを実行`https://localhost:44398`します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="a6a04-366">F12 ツールでブラウザを使用する場合:</span><span class="sxs-lookup"><span data-stu-id="a6a04-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="a6a04-367">**[値**] ボタンを選択し、[**ネットワーク**] タブでヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="a6a04-368">PUT**テスト**ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-368">Select the **PUT test** button.</span></span> <span data-ttu-id="a6a04-369">[OPTIONS 要求の表示](#options)手順については、「OPTIONS 要求の表示」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="a6a04-370">**PUT テスト**では、オプションプリフライト要求と PUT 要求の 2 つの要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="a6a04-371">失敗した**`GetValues2 [DisableCors]`** CORS 要求をトリガーするボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="a6a04-372">ドキュメントで説明したように、応答は 200 成功を返しますが、CORS 要求は行いません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="a6a04-373">**[コンソール**]タブを選択して、CORS エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="a6a04-374">ブラウザによっては、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="a6a04-375">ORIGIN`'https://cors1.azurewebsites.net/api/values/GetValues2'``'https://cors3.azurewebsites.net'`からのフェッチへのアクセスが CORS ポリシーによってブロックされました: 要求されたリソースに 'アクセス制御-許可-元の元のヘッダーがありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="a6a04-376">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="a6a04-377">CORS 対応のエンドポイントは[、curl](https://curl.haxx.se/) [、Fiddler](https://www.telerik.com/fiddler) [、Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="a6a04-378">ツールを使用する場合、ヘッダーで指定された要求の発信`Origin`元は、要求を受け取るホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="a6a04-379">要求がヘッダーの値に基づいて*クロスオリジン*でない場合: `Origin`</span><span class="sxs-lookup"><span data-stu-id="a6a04-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="a6a04-380">要求を処理するために CORS ミドルウェアは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="a6a04-381">CORS ヘッダーは応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="a6a04-382">次のコマンドは`curl`、情報を含む OPTIONS 要求を発行するために使用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="a6a04-383">エンドポイント ルーティングを使用して CORS をテストし、[HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="a6a04-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="a6a04-384">現在の使用を使用して`RequireCors`エンドポイントごとに CORS を有効にしても、[自動プリフライト要求](#apf)はサポート***されません***。</span><span class="sxs-lookup"><span data-stu-id="a6a04-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="a6a04-385">エンドポイント ルーティングを使用して[CORS を有効にする次のコードを](#ecors)検討してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="a6a04-386">テスト用`TodoItems1Controller`のエンドポイントを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="a6a04-387">展開された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[テスト ページ](https://cors1.azurewebsites.net/test?number=1)から、上記のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="a6a04-388">エンドポイントがプリフライト要求を持ち、応答するため **、[EnableCors]** ボタンと GET `[EnableCors]` **[EnableCors]** ボタンは成功します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="a6a04-389">他のエンドポイントは失敗します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-389">The other endpoints fails.</span></span> <span data-ttu-id="a6a04-390">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)が送信するため **、GET**ボタンが失敗します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="a6a04-391">以下`TodoItems2Controller`に、同様のエンドポイントを示しますが、OPTIONS 要求に応答する明示的なコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a6a04-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="a6a04-392">展開されたサンプルの[テスト ページ](https://cors1.azurewebsites.net/test?number=2)から、上記のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="a6a04-393">[**コントローラ**] ドロップダウン リストで、[**プリフライト**] を選択し、[**コントローラの設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="a6a04-394">`TodoItems2Controller`エンドポイントへの CORS 呼び出しはすべて成功します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6a04-395">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a6a04-395">Additional resources</span></span>

* [<span data-ttu-id="a6a04-396">クロスオリジンリソース共有(CORS)</span><span class="sxs-lookup"><span data-stu-id="a6a04-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="a6a04-397">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="a6a04-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a6a04-398">著者 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6a04-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a6a04-399">この記事では、ASP.NET コア アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="a6a04-400">ブラウザのセキュリティにより、Web ページが Web ページを提供したドメインとは異なるドメインに対して要求を行えないようにします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a6a04-401">この制限は、*同一生成元ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="a6a04-402">同一生成元ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取るのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a6a04-403">他のサイトがアプリに対してクロスオリジン要求を行うことを許可したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="a6a04-404">詳細については、 [Mozilla CORS の記事](https://developer.mozilla.org/docs/Web/HTTP/CORS)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="a6a04-405">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="a6a04-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="a6a04-406">サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="a6a04-407">セキュリティ機能**ではない**、CORS はセキュリティを緩和します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="a6a04-408">API は CORS を許可することで安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="a6a04-409">詳細については[、「CORS の仕組み](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="a6a04-410">サーバーが、一部のクロスオリジン要求を許可し、その他の要求を拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="a6a04-411">[JSONP](/dotnet/framework/wcf/samples/jsonp)などの以前の手法よりも安全で柔軟性があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="a6a04-412">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a6a04-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="a6a04-413">同じ起源</span><span class="sxs-lookup"><span data-stu-id="a6a04-413">Same origin</span></span>

<span data-ttu-id="a6a04-414">2 つの URL が同じスキーム、ホスト、およびポートを持つ場合は、同じオリジンを持ちます ([RFC 6454](https://tools.ietf.org/html/rfc6454))。</span><span class="sxs-lookup"><span data-stu-id="a6a04-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="a6a04-415">これらの 2 つの URL のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="a6a04-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="a6a04-416">これらの URL のオリジンは、前の 2 つの URL とは異なります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="a6a04-417">`https://example.net`&ndash;異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="a6a04-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="a6a04-418">`https://www.example.com/foo.html`&ndash;異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="a6a04-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="a6a04-419">`http://example.com/foo.html`&ndash;異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="a6a04-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="a6a04-420">`https://example.com:9000/foo.html`&ndash;異なるポート</span><span class="sxs-lookup"><span data-stu-id="a6a04-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="a6a04-421">オリジンを比較する際に、このポートは考慮されません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="a6a04-422">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="a6a04-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="a6a04-423">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="a6a04-424">次のコードでは、指定したオリジンを持つアプリ全体の CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="a6a04-425">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-425">The preceding code:</span></span>

* <span data-ttu-id="a6a04-426">ポリシー名を "myAllowSpecificOrigins"\_に設定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="a6a04-427">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="a6a04-428">拡張メソッド<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>を呼び出して、CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="a6a04-429"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="a6a04-430">ラムダはオブジェクト<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="a6a04-431">などの`WithOrigins`[構成オプション](#cors-policy-options)については、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="a6a04-432">メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しは、アプリのサービス コンテナーに CORS サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="a6a04-433">詳細については、このドキュメントの[CORS ポリシー オプション](#cpo)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="a6a04-434">メソッド<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>は、次のコードに示すように、メソッドをチェインできます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="a6a04-435">注意: URL**not**に末尾のスラッシュ (`/`) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="a6a04-436">URL が で`/`終わる場合、比較`false`は返され、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="a6a04-437">次のコードでは、CORS ミドルウェアを介してすべてのアプリ エンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="a6a04-438">注:`UseCors`前に`UseMvc`呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="a6a04-439">ページ/コント ローラー/アクション レベルで CORS ポリシーを適用するには[、Razor ページ、コント ローラー、およびアクション メソッド](#ecors)で CORS を有効にするを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="a6a04-440">上記のコードと同様のコードのテスト手順については、「[テスト CORS」](#test)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="a6a04-441">属性を使用した CORS の有効化</span><span class="sxs-lookup"><span data-stu-id="a6a04-441">Enable CORS with attributes</span></span>

<span data-ttu-id="a6a04-442">[ &lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="a6a04-443">この`[EnableCors]`属性は、すべての終点ではなく、選択した終点に対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="a6a04-444">既定`[EnableCors]`のポリシーを指定し、`[EnableCors("{Policy String}")]`ポリシーを指定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="a6a04-445">属性`[EnableCors]`は、次の場合に適用できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="a6a04-446">カミソリページ`PageModel`</span><span class="sxs-lookup"><span data-stu-id="a6a04-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="a6a04-447">コントローラー</span><span class="sxs-lookup"><span data-stu-id="a6a04-447">Controller</span></span>
* <span data-ttu-id="a6a04-448">コントローラアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="a6a04-448">Controller action method</span></span>

<span data-ttu-id="a6a04-449">属性を使用して、コントローラ/ページモデル/アクションに異なるポリシー`[EnableCors]`を適用できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="a6a04-450">属性が`[EnableCors]`コントローラ/ページ モデル/アクション メソッドに適用され、CORS がミドルウェアで有効になっている場合、***両方***のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="a6a04-451">ポリシーを組み合わせることはお勧め***しません***。</span><span class="sxs-lookup"><span data-stu-id="a6a04-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="a6a04-452">属性または`[EnableCors]`ミドルウェアを使用します。**not both**</span><span class="sxs-lookup"><span data-stu-id="a6a04-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="a6a04-453">を使用`[EnableCors]`する場合は、既定のポリシーを定義**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="a6a04-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="a6a04-454">次のコードでは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="a6a04-455">次のコードでは、CORS のデフォルト ポリシーと`"AnotherPolicy"`名前のポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="a6a04-456">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="a6a04-456">Disable CORS</span></span>

<span data-ttu-id="a6a04-457">[ &lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラ/ページ モデル/アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="a6a04-458">CORS ポリシー オプション</span><span class="sxs-lookup"><span data-stu-id="a6a04-458">CORS policy options</span></span>

<span data-ttu-id="a6a04-459">このセクションでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="a6a04-460">許可された原点を設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="a6a04-461">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="a6a04-462">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="a6a04-463">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="a6a04-464">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="a6a04-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="a6a04-465">プリフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="a6a04-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>が で`Startup.ConfigureServices`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a6a04-467">いくつかのオプションについては、[最初に「CORS の仕組み](#how-cors)」のセクションを読んでも役に立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="a6a04-468">許可された原点を設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-468">Set the allowed origins</span></span>

<span data-ttu-id="a6a04-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;任意のスキーム ( または`http``https`) を持つすべてのオリジンからの CORS 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="a6a04-470">`AllowAnyOrigin`*どのウェブサイトも*アプリにクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a6a04-471">指定され`AllowAnyOrigin`、`AllowCredentials`安全でない構成であり、クロスサイトリクエストフォージェリにつながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="a6a04-472">セキュリティで保護されたアプリの場合、クライアントがサーバー リソースにアクセスする権限を持つ必要がある場合は、発信元の正確な一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="a6a04-473">`AllowAnyOrigin`はプリフライト要求とヘッダー`Access-Control-Allow-Origin`に影響します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="a6a04-474">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a6a04-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;ポリシーの<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>プロパティを、オリジンが許可されているかどうかを評価するときに、設定されたワイルドカード ドメインとオリジンが一致するように設定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="a6a04-476">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="a6a04-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="a6a04-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="a6a04-478">任意の HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="a6a04-479">プリフライト要求とヘッダーに`Access-Control-Allow-Methods`影響します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="a6a04-480">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="a6a04-481">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-481">Set the allowed request headers</span></span>

<span data-ttu-id="a6a04-482">特定のヘッダーを CORS 要求で送信できるようにするには *、author 要求ヘッダー*と呼<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>びます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="a6a04-483">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="a6a04-484">この設定は、プリフライト要求と`Access-Control-Request-Headers`ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="a6a04-485">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a6a04-486">CORS ミドルウェアでは、CorsPolicy.Headers で構成されている値`Access-Control-Request-Headers`に関係なく、常に 4 つのヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="a6a04-487">このヘッダーのリストには、次の項目が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="a6a04-488">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="a6a04-489">CORS ミドルウェアは、常にホワイトリストに登録されているため`Content-Language`、次の要求ヘッダーを使用してプリフライト要求に正常に応答します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="a6a04-490">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-490">Set the exposed response headers</span></span>

<span data-ttu-id="a6a04-491">既定では、ブラウザーは、すべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="a6a04-492">詳細については、「 [W3C クロス オリジン リソース共有 (用語集): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="a6a04-493">デフォルトで使用可能な応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a6a04-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="a6a04-494">CORS 仕様では、これらのヘッダーの*単純な応答ヘッダーを*呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="a6a04-495">他のヘッダーをアプリで使用できるようにするには、次の<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="a6a04-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="a6a04-496">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="a6a04-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="a6a04-497">資格情報には、CORS 要求での特別な処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="a6a04-498">既定では、ブラウザーはクロスオリジン要求を含む資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="a6a04-499">資格情報には、Cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="a6a04-500">クロスオリジン要求を使用して資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials``true`に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="a6a04-501">直接`XMLHttpRequest`使用:</span><span class="sxs-lookup"><span data-stu-id="a6a04-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="a6a04-502">jQuery を使用する:</span><span class="sxs-lookup"><span data-stu-id="a6a04-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="a6a04-503">フェッチ[API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用 :</span><span class="sxs-lookup"><span data-stu-id="a6a04-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="a6a04-504">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-504">The server must allow the credentials.</span></span> <span data-ttu-id="a6a04-505">クロスオリジンの資格情報を許可するには、次<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="a6a04-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="a6a04-506">HTTP 応答には`Access-Control-Allow-Credentials`ヘッダーが含まれており、サーバーがクロスオリジン要求の資格情報を許可することをブラウザーに伝えます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="a6a04-507">ブラウザーが資格情報を送信しても、応答に有効な`Access-Control-Allow-Credentials`ヘッダーが含まれていない場合、ブラウザーはアプリに対する応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="a6a04-508">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="a6a04-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="a6a04-509">別のドメインの Web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="a6a04-510">CORS 仕様では、ヘッダーが存在する場合`"*"`、(すべての起点) に原点`Access-Control-Allow-Credentials`を設定することは無効であるとも述べています。</span><span class="sxs-lookup"><span data-stu-id="a6a04-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="a6a04-511">プリフライトリクエスト</span><span class="sxs-lookup"><span data-stu-id="a6a04-511">Preflight requests</span></span>

<span data-ttu-id="a6a04-512">一部の CORS 要求では、ブラウザーは実際の要求を行う前に追加の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="a6a04-513">この要求は *、プリフライト要求*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="a6a04-514">次の条件に該当する場合、ブラウザはプリフライトリクエストをスキップできます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="a6a04-515">要求メソッドは、GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="a6a04-516">アプリ`Accept`は、 、 、 `Accept-Language`、 、、`Content-Language``Content-Type`または`Last-Event-ID`以外の要求ヘッダーを設定しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="a6a04-517">ヘッダー`Content-Type`が設定されている場合、次のいずれかの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="a6a04-518">クライアント要求に設定された要求ヘッダーのルールは、アプリケーションがオブジェクトを呼び出`setRequestHeader`すことによって設定するヘッダーに`XMLHttpRequest`適用されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="a6a04-519">CORS 仕様では、これらのヘッダー*の作成者要求ヘッダーを*呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="a6a04-520">ルールは、 、 、 など、`User-Agent``Host`ブラウザで設定できるヘッダーには適用されません。 `Content-Length`</span><span class="sxs-lookup"><span data-stu-id="a6a04-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="a6a04-521">プリフライト要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="a6a04-522">プレフライト要求では、HTTP OPTIONS メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="a6a04-523">これには、次の 2 つの特別なヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-523">It includes two special headers:</span></span>

* <span data-ttu-id="a6a04-524">`Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="a6a04-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="a6a04-525">`Access-Control-Request-Headers`: アプリが実際の要求に設定する要求ヘッダーの一覧。</span><span class="sxs-lookup"><span data-stu-id="a6a04-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="a6a04-526">前述のように、ブラウザーが設定するヘッダーは含みません`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="a6a04-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="a6a04-527">適切なポリシーで CORS が有効になっている場合、通常、ASP.NETコアは CORS プリフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="a6a04-528">[プリフライト要求の場合は[HttpOptions] 属性を](#pro)参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="a6a04-529">CORS プリフライト要求には、実際`Access-Control-Request-Headers`の要求と共に送信されるヘッダーをサーバーに示すヘッダーが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="a6a04-530">特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="a6a04-531">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="a6a04-532">ブラウザは、設定方法に完全に一貫`Access-Control-Request-Headers`しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="a6a04-533">ヘッダーを ( または を使用`"*"`<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>) 以外に設定する場合は`Accept`、 `Content-Type`、 `Origin`、および 、 、および をサポートするカスタム ヘッダーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="a6a04-534">次に、プリフライト要求に対する応答の例を示します (サーバーが要求を許可すると仮定します)。</span><span class="sxs-lookup"><span data-stu-id="a6a04-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="a6a04-535">応答には、`Access-Control-Allow-Methods`許可されたメソッドと、許可されたヘッダーをリストする`Access-Control-Allow-Headers`ヘッダー (オプション) を示すヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="a6a04-536">プリフライト要求が成功すると、ブラウザは実際のリクエストを送信します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="a6a04-537">プリフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーは返しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="a6a04-538">したがって、ブラウザーは、クロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="a6a04-539">プリフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="a6a04-539">Set the preflight expiration time</span></span>

<span data-ttu-id="a6a04-540">ヘッダー`Access-Control-Max-Age`は、プリフライト要求に対する応答をキャッシュできる時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="a6a04-541">このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="a6a04-542">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="a6a04-542">How CORS works</span></span>

<span data-ttu-id="a6a04-543">このセクションでは、HTTP メッセージのレベルで[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求で何が起こるかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="a6a04-544">CORS はセキュリティ機能**ではありません**。</span><span class="sxs-lookup"><span data-stu-id="a6a04-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="a6a04-545">CORS は、サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="a6a04-546">たとえば、悪意のあるアクターがサイトに対して[クロスサイト スクリプティング (XSS) を](xref:security/cross-site-scripting)使用し、CORS 対応サイトに対してクロスサイト要求を実行して情報を盗む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="a6a04-547">API は CORS を許可することで安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="a6a04-548">CORS を強制するのはクライアント (ブラウザー) の管理です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="a6a04-549">サーバーは要求を実行し、応答を返します、それはエラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="a6a04-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="a6a04-550">たとえば、次のツールのどれでもサーバーの応答を表示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="a6a04-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="a6a04-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="a6a04-552">Postman</span><span class="sxs-lookup"><span data-stu-id="a6a04-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="a6a04-553">.NET Http クライアント</span><span class="sxs-lookup"><span data-stu-id="a6a04-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="a6a04-554">アドレス バーに URL を入力して Web ブラウザを表示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="a6a04-555">これは、ブラウザがクロスオリジン[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行することを許可するサーバーの方法です。</span><span class="sxs-lookup"><span data-stu-id="a6a04-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="a6a04-556">ブラウザ (CORS を使用しない) は、クロスオリジン要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="a6a04-557">CORS より前では[、JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)はこの制限を回避するために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="a6a04-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="a6a04-558">JSONP は XHR を使用せず、`<script>`タグを使用して応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="a6a04-559">スクリプトは、クロスオリジンでロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="a6a04-560">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にするいくつかの新しい HTTP ヘッダーが導入されました。</span><span class="sxs-lookup"><span data-stu-id="a6a04-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="a6a04-561">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="a6a04-562">カスタム JavaScript コードは、CORS を有効にする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="a6a04-563">次に、クロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="a6a04-564">ヘッダー`Origin`は、要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="a6a04-565">ヘッダー`Origin`は必須であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="a6a04-566">サーバーが要求を許可する場合、応答の`Access-Control-Allow-Origin`ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="a6a04-567">このヘッダーの値は、要求の`Origin`ヘッダーと一致するか、ワイルドカード値`"*"`で、任意のオリジンが許可されていることを意味します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="a6a04-568">応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="a6a04-569">具体的には、ブラウザーは要求を拒否します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="a6a04-570">サーバーが成功した応答を返した場合でも、ブラウザーはクライアント アプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="a6a04-571">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="a6a04-571">Test CORS</span></span>

<span data-ttu-id="a6a04-572">CORS をテストするには:</span><span class="sxs-lookup"><span data-stu-id="a6a04-572">To test CORS:</span></span>

1. <span data-ttu-id="a6a04-573">[API プロジェクトを作成する](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="a6a04-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="a6a04-574">または、[サンプルをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="a6a04-575">このドキュメントの方法のいずれかを使用して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="a6a04-576">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="a6a04-577">`WithOrigins("https://localhost:<port>");`ダウンロード サンプル[コード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)に似たサンプル アプリのテストにのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="a6a04-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="a6a04-578">Web アプリ プロジェクト (Razor ページまたは MVC) を作成します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="a6a04-579">サンプルでは、Razor ページを使用します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="a6a04-580">API プロジェクトと同じソリューションで Web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="a6a04-581">次の強調表示されたコードを*Index.cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="a6a04-582">上記のコードで、デプロイされた`url: 'https://<web app>.azurewebsites.net/api/values/1',`アプリの URL で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="a6a04-583">API プロジェクトをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-583">Deploy the API project.</span></span> <span data-ttu-id="a6a04-584">たとえば、 [Azure にデプロイします](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="a6a04-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="a6a04-585">デスクトップから Razor ページまたは MVC アプリを実行し、[**テスト**] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="a6a04-586">F12 ツールを使用して、エラー メッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="a6a04-587">ローカルホストのオリジンをアプリ`WithOrigins`から削除し、アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="a6a04-588">または、別のポートでクライアント アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="a6a04-589">たとえば、Visual Studio から実行します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="a6a04-590">クライアント アプリでテストします。</span><span class="sxs-lookup"><span data-stu-id="a6a04-590">Test with the client app.</span></span> <span data-ttu-id="a6a04-591">CORS のエラーはエラーを返しますが、エラー メッセージは JavaScript で使用できません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="a6a04-592">F12 ツールのコンソール タブを使用して、エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="a6a04-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="a6a04-593">ブラウザーによっては、次のようなエラー (F12 ツール コンソール) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="a6a04-594">マイクロソフトエッジを使用する:</span><span class="sxs-lookup"><span data-stu-id="a6a04-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="a6a04-595">**SEC7120: [CORS]`https://localhost:44375`オリジンが、`https://localhost:44375`クロスオリジンリソースのアクセス制御許可-オリジン応答ヘッダーで見つかりませんでした。`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="a6a04-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="a6a04-596">クロムの使用:</span><span class="sxs-lookup"><span data-stu-id="a6a04-596">Using Chrome:</span></span>

     <span data-ttu-id="a6a04-597">**元`https://webapi.azurewebsites.net/api/values/1``https://localhost:44375`の XMLHttpRequest へのアクセスが CORS ポリシーによってブロックされました: 要求されたリソースに 'アクセス制御-許可-元のヘッダー' が存在しません。**</span><span class="sxs-lookup"><span data-stu-id="a6a04-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="a6a04-598">CORS 対応のエンドポイントは[、Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="a6a04-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="a6a04-599">ツールを使用する場合、ヘッダーで指定された要求の発信`Origin`元は、要求を受け取るホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="a6a04-600">要求がヘッダーの値に基づいて*クロスオリジン*でない場合: `Origin`</span><span class="sxs-lookup"><span data-stu-id="a6a04-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="a6a04-601">要求を処理するために CORS ミドルウェアは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="a6a04-602">CORS ヘッダーは応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="a6a04-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="a6a04-603">IIS の CORS</span><span class="sxs-lookup"><span data-stu-id="a6a04-603">CORS in IIS</span></span>

<span data-ttu-id="a6a04-604">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合は、Windows 認証の前に CORS を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="a6a04-605">このシナリオをサポートするには[、IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールし、アプリ用に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6a04-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6a04-606">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a6a04-606">Additional resources</span></span>

* [<span data-ttu-id="a6a04-607">クロスオリジンリソース共有(CORS)</span><span class="sxs-lookup"><span data-stu-id="a6a04-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="a6a04-608">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="a6a04-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
