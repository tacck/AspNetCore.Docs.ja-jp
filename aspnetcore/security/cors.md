---
<span data-ttu-id="2488f-101">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="2488f-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2488f-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2488f-102">'Blazor'</span></span>
- <span data-ttu-id="2488f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2488f-103">'Identity'</span></span>
- <span data-ttu-id="2488f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2488f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2488f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2488f-105">'Razor'</span></span>
- <span data-ttu-id="2488f-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2488f-106">'SignalR' uid:</span></span> 

---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="2488f-107">ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする</span><span class="sxs-lookup"><span data-stu-id="2488f-107">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2488f-108">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="2488f-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="2488f-109">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-109">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="2488f-110">ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="2488f-110">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="2488f-111">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-111">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="2488f-112">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="2488f-112">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="2488f-113">場合によっては、他のサイトがアプリに対してクロスオリジン要求を行うことを許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-113">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="2488f-114">詳細については、「 [MOZILLA CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-114">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="2488f-115">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="2488f-115">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="2488f-116">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="2488f-116">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="2488f-117">は、CORS 緩和され security のセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-117">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="2488f-118">CORS を許可すると、API の安全性が向上しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-118">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="2488f-119">詳細については、「 [CORS のしくみ](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-119">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="2488f-120">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="2488f-120">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="2488f-121">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="2488f-121">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="2488f-122">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2488f-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="2488f-123">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="2488f-123">Same origin</span></span>

<span data-ttu-id="2488f-124">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="2488f-124">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="2488f-125">この2つの Url のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="2488f-125">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="2488f-126">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="2488f-126">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="2488f-127">`https://example.net`: 異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="2488f-127">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="2488f-128">`https://www.example.com/foo.html`: 異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="2488f-128">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="2488f-129">`http://example.com/foo.html`: 異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="2488f-129">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="2488f-130">`https://example.com:9000/foo.html`: 別のポート</span><span class="sxs-lookup"><span data-stu-id="2488f-130">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="2488f-131">CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="2488f-131">Enable CORS</span></span>

<span data-ttu-id="2488f-132">CORS を有効にするには、次の3つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-132">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="2488f-133">[名前付きポリシー](#np)または既定の[ポリシー](#dp)を使用するミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="2488f-133">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="2488f-134">[エンドポイントルーティング](#ecors)を使用する。</span><span class="sxs-lookup"><span data-stu-id="2488f-134">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="2488f-135">[[Enablecors]](#attr)属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-135">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="2488f-136">名前付きポリシーで[[Enablecors]](#attr)属性を使用すると、CORS をサポートするエンドポイントを制限するための最も細かい制御が可能になります。</span><span class="sxs-lookup"><span data-stu-id="2488f-136">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="2488f-137">各方法の詳細については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-137">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="2488f-138">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="2488f-138">CORS with named policy and middleware</span></span>

<span data-ttu-id="2488f-139">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="2488f-139">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="2488f-140">次のコードは、指定されたオリジンを持つすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-140">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="2488f-141">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2488f-141">The preceding code:</span></span>

* <span data-ttu-id="2488f-142">ポリシー名をに設定 `_myAllowSpecificOrigins` します。</span><span class="sxs-lookup"><span data-stu-id="2488f-142">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="2488f-143">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="2488f-143">The policy name is arbitrary.</span></span>
* <span data-ttu-id="2488f-144">拡張メソッドを呼び出し、 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> CORS ポリシーを指定し `_myAllowSpecificOrigins` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-144">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="2488f-145">`UseCors`CORS ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="2488f-145">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="2488f-146">への呼び出しは `UseCors` `UseRouting` 、の後、の前に配置する必要があり `UseAuthorization` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-146">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="2488f-147">詳細については、「[ミドルウェアの順序](xref:fundamentals/middleware/index#middleware-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-147">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="2488f-148"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2488f-148">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="2488f-149">ラムダはオブジェクトを受け取り <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-149">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="2488f-150">などの[構成オプション](#cors-policy-options)に `WithOrigins` ついては、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-150">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="2488f-151">`_myAllowSpecificOrigins`すべてのコントローラーエンドポイントに対して CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="2488f-151">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="2488f-152">特定のエンドポイントに CORS ポリシーを適用するには、「[エンドポイントルーティング](#ecors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-152">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="2488f-153">エンドポイントルーティングでは、CORS ミドルウェアをとの呼び出しの間で実行するように構成する**必要があり** `UseRouting` `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-153">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="2488f-154">前のコードのようなコードをテストする方法については、「[テスト CORS](#testc) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-154">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="2488f-155">メソッド呼び出しによって、 <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> アプリのサービスコンテナーに CORS サービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-155">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="2488f-156">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-156">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="2488f-157">メソッドは、 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 次のコードに示すように、連結できます。</span><span class="sxs-lookup"><span data-stu-id="2488f-157">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="2488f-158">注: 指定された URL に末尾にスラッシュ () を含めることは**できません** `/` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-158">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="2488f-159">URL がで終了した場合 `/` 、比較はを返し、 `false` ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="2488f-159">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="2488f-160">既定のポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="2488f-160">CORS with default policy and middleware</span></span>

<span data-ttu-id="2488f-161">次の強調表示されたコードは、既定の CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="2488f-161">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="2488f-162">上記のコードは、すべてのコントローラーエンドポイントに既定の CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-162">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="2488f-163">エンドポイント ルーティングで CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="2488f-163">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="2488f-164">現在、を使用して、エンドポイントごとに CORS を有効にする `RequireCors` ことは、[自動プレフライト要求](#apf)をサポートして**いません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-164">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="2488f-165">詳細については、こちらの[GitHub の問題](https://github.com/dotnet/aspnetcore/issues/20709)を参照してください。また、[エンドポイントルーティングと [HttpOptions] を使用](#tcer)して CORS をテストしてください。</span><span class="sxs-lookup"><span data-stu-id="2488f-165">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="2488f-166">エンドポイントルーティングを使用すると、一連の拡張メソッドを使用して、エンドポイントごとに CORS を有効にすることができ <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-166">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="2488f-167">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2488f-167">In the preceding code:</span></span>

* <span data-ttu-id="2488f-168">`app.UseCors`CORS ミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="2488f-168">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="2488f-169">既定のポリシーが構成されていないため、 `app.UseCors()` 単独で CORS を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="2488f-169">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="2488f-170">`/echo`およびコントローラーエンドポイントは、指定されたポリシーを使用して、クロスオリジン要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="2488f-170">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="2488f-171">`/echo2`既定のポリシーが指定されていないため、と Razor Pages エンドポイントはクロスオリジン要求を許可し**ません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-171">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="2488f-172">[[Disablecors]](#dc)属性では、を使用してエンドポイントルーティングによって有効にされた CORS は無効になり**ません** `RequireCors` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-172">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="2488f-173">前述のようなコードをテストする方法については、「[エンドポイントルーティングを使用した CORS のテスト」および「[HttpOptions]](#tcer) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-173">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="2488f-174">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="2488f-174">Enable CORS with attributes</span></span>

<span data-ttu-id="2488f-175">[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性を使用して cors を有効にし、cors を必要とするエンドポイントのみに名前付きポリシーを適用することで、最も細かい制御が可能になります。</span><span class="sxs-lookup"><span data-stu-id="2488f-175">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="2488f-176">[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用するための代替手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="2488f-176">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="2488f-177">属性は、 `[EnableCors]` すべてのエンドポイントではなく、選択したエンドポイントに対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="2488f-177">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="2488f-178">`[EnableCors]`既定のポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-178">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="2488f-179">`[EnableCors("{Policy String}")]`名前付きポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-179">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="2488f-180">`[EnableCors]`属性は次のものに適用できます。</span><span class="sxs-lookup"><span data-stu-id="2488f-180">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="2488f-181">改`PageModel`</span><span class="sxs-lookup"><span data-stu-id="2488f-181"> Page `PageModel`</span></span>
* <span data-ttu-id="2488f-182">コントローラー</span><span class="sxs-lookup"><span data-stu-id="2488f-182">Controller</span></span>
* <span data-ttu-id="2488f-183">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="2488f-183">Controller action method</span></span>

<span data-ttu-id="2488f-184">属性を使用して、さまざまなポリシーをコントローラー、ページモデル、またはアクションメソッドに適用でき `[EnableCors]` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-184">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="2488f-185">`[EnableCors]`属性がコントローラー、ページモデル、またはアクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、**両方**のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-185">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="2488f-186">**ポリシーを組み合わせることはお勧めしません。** `[EnableCors]` **同じアプリ内ではなく、属性またはミドルウェアを使用します。**</span><span class="sxs-lookup"><span data-stu-id="2488f-186">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="2488f-187">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-187">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="2488f-188">次のコードでは、2つの CORS ポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2488f-188">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="2488f-189">CORS 要求を制限する最も細かい制御:</span><span class="sxs-lookup"><span data-stu-id="2488f-189">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="2488f-190">`[EnableCors("MyPolicy")]`名前付きポリシーでを使用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-190">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="2488f-191">既定のポリシーは定義しないでください。</span><span class="sxs-lookup"><span data-stu-id="2488f-191">Don't define a default policy.</span></span>
* <span data-ttu-id="2488f-192">[エンドポイントルーティング](#ecors)を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="2488f-192">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="2488f-193">次のセクションのコードは、上記のリストを満たしています。</span><span class="sxs-lookup"><span data-stu-id="2488f-193">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="2488f-194">前のコードのようなコードをテストする方法については、「[テスト CORS](#testc) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-194">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="2488f-195">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="2488f-195">Disable CORS</span></span>

<span data-ttu-id="2488f-196">[[Disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、[エンドポイントルーティング](#ecors)によって有効にされた CORS を無効にし**ません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-196">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="2488f-197">次のコードでは、CORS ポリシーを定義してい `"MyPolicy"` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-197">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="2488f-198">次のコードは、アクションの CORS を無効にし `GetValues2` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-198">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="2488f-199">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2488f-199">The preceding code:</span></span>

* <span data-ttu-id="2488f-200">[エンドポイントルーティング](#ecors)で CORS を有効にしません。</span><span class="sxs-lookup"><span data-stu-id="2488f-200">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="2488f-201">[既定の CORS ポリシー](#dp)は定義されていません。</span><span class="sxs-lookup"><span data-stu-id="2488f-201">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="2488f-202">では、 [[Enablecors ("MyPolicy")]](#attr)を使用して `"MyPolicy"` 、コントローラーの CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="2488f-202">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="2488f-203">メソッドの CORS を無効に `GetValues2` します。</span><span class="sxs-lookup"><span data-stu-id="2488f-203">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="2488f-204">前のコードをテストする手順については、「[テスト CORS](#testc) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-204">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="2488f-205">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="2488f-205">CORS policy options</span></span>

<span data-ttu-id="2488f-206">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-206">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="2488f-207">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="2488f-207">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="2488f-208">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-208">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="2488f-209">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-209">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="2488f-210">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-210">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="2488f-211">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="2488f-211">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="2488f-212">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-212">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="2488f-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>はで呼び出され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2488f-214">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-214">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="2488f-215">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="2488f-215">Set the allowed origins</span></span>

<span data-ttu-id="2488f-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: 任意のスキーム (または) を持つすべてのオリジンからの CORS 要求を許可 `http` `https` します。</span><span class="sxs-lookup"><span data-stu-id="2488f-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="2488f-217">`AllowAnyOrigin`*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="2488f-217">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="2488f-218">とを指定すると、 `AllowAnyOrigin` `AllowCredentials` 安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-218">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="2488f-219">アプリが両方の方法で構成されている場合、CORS サービスは無効な CORS 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="2488f-219">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="2488f-220">`AllowAnyOrigin`プレフライト要求とヘッダーに影響し `Access-Control-Allow-Origin` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-220">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="2488f-221">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="2488f-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインとオリジンが一致するようにするための関数として、ポリシーのプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="2488f-223">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-223">Set the allowed HTTP methods</span></span>

<span data-ttu-id="2488f-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="2488f-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="2488f-225">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="2488f-225">Allows any HTTP method:</span></span>
* <span data-ttu-id="2488f-226">プレフライト要求とヘッダーに影響し `Access-Control-Allow-Methods` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-226">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="2488f-227">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-227">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="2488f-228">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-228">Set the allowed request headers</span></span>

<span data-ttu-id="2488f-229">CORS 要求で特定のヘッダーを送信できるようにするには、 [author 要求ヘッダー](https://xhr.spec.whatwg.org/#request)と呼ばれるを呼び出し、 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-229">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="2488f-230">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-230">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="2488f-231">`AllowAnyHeader`プレフライト要求および[アクセス制御-要求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)ヘッダーヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="2488f-231">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="2488f-232">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-232">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="2488f-233">によって指定された特定のヘッダーに対して CORS ミドルウェアポリシーが一致するのは、ヘッダーがで記述されているヘッダーと `WithHeaders` 完全に一致する場合のみです `Access-Control-Request-Headers` `WithHeaders` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-233">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="2488f-234">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="2488f-234">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="2488f-235">CORS ミドルウェアは、次の要求ヘッダーを使用してプレフライト要求を拒否し `Content-Language` ます。 ([headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) は、には記載されていないため `WithHeaders` です。</span><span class="sxs-lookup"><span data-stu-id="2488f-235">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="2488f-236">アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-236">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="2488f-237">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-237">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="2488f-238">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-238">Set the exposed response headers</span></span>

<span data-ttu-id="2488f-239">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-239">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="2488f-240">詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-240">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="2488f-241">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2488f-241">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="2488f-242">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2488f-242">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="2488f-243">アプリで他のヘッダーを使用できるようにするには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-243">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="2488f-244">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="2488f-244">Credentials in cross-origin requests</span></span>

<span data-ttu-id="2488f-245">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-245">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="2488f-246">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-246">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="2488f-247">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-247">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="2488f-248">クロスオリジン要求で資格情報を送信するには、クライアントがに設定されている必要があり `XMLHttpRequest.withCredentials` `true` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-248">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="2488f-249">直接の使用 `XMLHttpRequest` :</span><span class="sxs-lookup"><span data-stu-id="2488f-249">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="2488f-250">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="2488f-250">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="2488f-251">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="2488f-251">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="2488f-252">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-252">The server must allow the credentials.</span></span> <span data-ttu-id="2488f-253">クロスオリジンの資格情報を許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-253">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="2488f-254">HTTP 応答には、 `Access-Control-Allow-Credentials` サーバーがクロスオリジン要求に対して資格情報を許可することをブラウザーに示すヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2488f-254">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="2488f-255">ブラウザーが資格情報を送信しても、応答に有効なヘッダーが含まれていない場合 `Access-Control-Allow-Credentials` 、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="2488f-255">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="2488f-256">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="2488f-256">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="2488f-257">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="2488f-257">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="2488f-258">また、CORS 仕様では、ヘッダーが存在する場合、[オリジン] を [(すべてのオリジン)] に設定することもでき `"*"` `Access-Control-Allow-Credentials` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-258">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="2488f-259">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="2488f-259">Preflight requests</span></span>

<span data-ttu-id="2488f-260">一部の CORS 要求については、ブラウザーは、実際の要求を行う前に、追加の[オプション](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="2488f-260">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="2488f-261">この要求は[プレフライト要求](https://developer.mozilla.org/docs/Glossary/Preflight_request)と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-261">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="2488f-262">次の**すべて**の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="2488f-262">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="2488f-263">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="2488f-263">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="2488f-264">アプリで `Accept` は、、、 `Accept-Language` `Content-Language` 、 `Content-Type` 、または `Last-Event-ID` 以外の要求ヘッダーは設定されません。</span><span class="sxs-lookup"><span data-stu-id="2488f-264">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="2488f-265">ヘッダーには、設定されて `Content-Type` いる場合、次のいずれかの値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-265">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="2488f-266">クライアント要求に対して設定された要求ヘッダーに適用される規則は、オブジェクトに対してを呼び出すことによって、アプリが設定するヘッダーに適用され `setRequestHeader` `XMLHttpRequest` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-266">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="2488f-267">CORS 仕様は、これらのヘッダー[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2488f-267">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="2488f-268">このルールは、ブラウザーが設定できるヘッダー (、、など) には適用されません `User-Agent` `Host` `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-268">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="2488f-269">次に示すのは、このドキュメントの「[テスト CORS](#testc) 」セクションの **[Put test]** ボタンから行ったプレフライト要求に似た応答の例です。</span><span class="sxs-lookup"><span data-stu-id="2488f-269">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="2488f-270">プレフライト要求では、 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-270">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="2488f-271">次のヘッダーが含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-271">It may include the following headers:</span></span>

* <span data-ttu-id="2488f-272">[アクセス制御要求メソッド](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="2488f-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="2488f-273">[アクセス制御要求ヘッダー](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): アプリが実際の要求に対して設定する要求ヘッダーのリスト。</span><span class="sxs-lookup"><span data-stu-id="2488f-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="2488f-274">前述のように、これにはブラウザーが設定するヘッダー (など) は含まれません `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-274">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="2488f-275">アクセス制御-許可-メソッド</span><span class="sxs-lookup"><span data-stu-id="2488f-275">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="2488f-276">プレフライト要求が拒否された場合、アプリは応答を返し `200 OK` ますが、CORS ヘッダーは設定しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-276">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="2488f-277">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-277">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="2488f-278">拒否されたプレフライト要求の例については、このドキュメントの「[テスト CORS](#testc) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-278">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="2488f-279">F12 ツールを使用すると、コンソールアプリには、ブラウザーに応じて次のいずれかのようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-279">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="2488f-280">Firefox: クロスオリジン要求がブロックされています: 同じオリジンポリシーで、でのリモートリソースの読み取りが許可されて `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` いません。</span><span class="sxs-lookup"><span data-stu-id="2488f-280">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="2488f-281">(理由: CORS 要求が失敗しました)。</span><span class="sxs-lookup"><span data-stu-id="2488f-281">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="2488f-282">詳細情報</span><span class="sxs-lookup"><span data-stu-id="2488f-282">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="2488f-283">Chromium ベース: オリジン ' ' からの ' ' でのフェッチへのアクセス https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net が CORS ポリシーによってブロックされました。プレフライト要求への応答がアクセス制御チェックに合格しません: 要求されたリソースに ' アクセス制御-許可-オリジン ' ヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-283">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="2488f-284">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="2488f-284">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="2488f-285">特定のヘッダーを許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-285">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="2488f-286">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-286">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="2488f-287">ブラウザーの設定方法が一貫していません `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-287">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="2488f-288">次のいずれかの場合:</span><span class="sxs-lookup"><span data-stu-id="2488f-288">If either:</span></span>

* <span data-ttu-id="2488f-289">ヘッダーは、`"*"`</span><span class="sxs-lookup"><span data-stu-id="2488f-289">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="2488f-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>が呼び出されます: 少なくとも、、、 `Accept` `Content-Type` `Origin` 、およびサポートするカスタムヘッダーを含めます。</span><span class="sxs-lookup"><span data-stu-id="2488f-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="2488f-291">自動プレフライト要求コード</span><span class="sxs-lookup"><span data-stu-id="2488f-291">Automatic preflight request code</span></span>

<span data-ttu-id="2488f-292">CORS ポリシーを適用する場合は、次のいずれかの方法を実行します。</span><span class="sxs-lookup"><span data-stu-id="2488f-292">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="2488f-293">でを呼び出すことにより、グローバルに `app.UseCors` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-293">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="2488f-294">属性を使用し `[EnableCors]` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-294">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="2488f-295">ASP.NET Core は、プレフライトオプション要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="2488f-295">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="2488f-296">現在、を使用して、エンドポイントごとに CORS を有効にする `RequireCors` ことは、自動プレフライト要求をサポートして**いません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-296">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="2488f-297">このドキュメントの「[テスト CORS](#testc) 」セクションでは、この動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-297">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="2488f-298">プレフライト要求の [HttpOptions] 属性</span><span class="sxs-lookup"><span data-stu-id="2488f-298">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="2488f-299">CORS が適切なポリシーで有効になっている場合、ASP.NET Core は通常、CORS プレフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="2488f-299">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="2488f-300">シナリオによっては、これが当てはまりません。</span><span class="sxs-lookup"><span data-stu-id="2488f-300">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="2488f-301">たとえば、CORS を[エンドポイントルーティングと共](#ecors)に使用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-301">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="2488f-302">次のコードでは、 [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)属性を使用して、OPTIONS 要求のエンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="2488f-302">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="2488f-303">前のコードをテストする手順については、「[エンドポイントルーティングを使用した CORS のテスト」および「[HttpOptions]](#tcer) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-303">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="2488f-304">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-304">Set the preflight expiration time</span></span>

<span data-ttu-id="2488f-305">ヘッダーは、 `Access-Control-Max-Age` プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-305">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="2488f-306">このヘッダーを設定するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-306">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="2488f-307">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="2488f-307">How CORS works</span></span>

<span data-ttu-id="2488f-308">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-308">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="2488f-309">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-309">CORS is **not** a security feature.</span></span> <span data-ttu-id="2488f-310">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="2488f-310">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="2488f-311">たとえば、悪意のあるアクターがサイトに対して[クロスサイトスクリプティング (XSS)](xref:security/cross-site-scripting)を使用して、CORS が有効になっているサイトに対してクロスサイト要求を実行し、情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="2488f-311">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="2488f-312">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="2488f-312">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="2488f-313">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="2488f-313">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="2488f-314">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="2488f-314">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="2488f-315">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-315">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="2488f-316">Fiddler</span><span class="sxs-lookup"><span data-stu-id="2488f-316">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="2488f-317">Postman</span><span class="sxs-lookup"><span data-stu-id="2488f-317">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="2488f-318">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="2488f-318">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="2488f-319">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="2488f-319">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="2488f-320">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="2488f-320">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="2488f-321">CORS のないブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="2488f-321">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="2488f-322">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="2488f-322">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="2488f-323">JSONP は XHR を使用しないので、タグを使用して `<script>` 応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="2488f-323">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="2488f-324">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="2488f-324">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="2488f-325">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="2488f-325">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="2488f-326">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-326">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="2488f-327">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2488f-327">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="2488f-328">配置された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の [[テストの配置] ボタン](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="2488f-328">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="2488f-329">次に、[[値](https://cors3.azurewebsites.net/)のテスト] ボタンからへのクロスオリジン要求の例を示し `https://cors1.azurewebsites.net/api/values` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-329">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="2488f-330">`Origin`ヘッダー:</span><span class="sxs-lookup"><span data-stu-id="2488f-330">The `Origin` header:</span></span>

* <span data-ttu-id="2488f-331">要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="2488f-331">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="2488f-332">は必須であり、ホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-332">Is required and must be different from the host.</span></span>

<span data-ttu-id="2488f-333">**一般的なヘッダー**</span><span class="sxs-lookup"><span data-stu-id="2488f-333">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="2488f-334">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="2488f-334">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="2488f-335">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="2488f-335">**Request headers**</span></span>

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

<span data-ttu-id="2488f-336">要求では、 `OPTIONS` サーバーは応答**Response headers**のヘッダーヘッダーを設定し `Access-Control-Allow-Origin: {allowed origin}` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-336">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="2488f-337">たとえば、デプロイされた[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[Delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) button 要求には、 `OPTIONS` 次のヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2488f-337">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="2488f-338">**一般的なヘッダー**</span><span class="sxs-lookup"><span data-stu-id="2488f-338">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="2488f-339">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="2488f-339">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="2488f-340">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="2488f-340">**Request headers**</span></span>

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

<span data-ttu-id="2488f-341">上記の**応答ヘッダー**では、サーバーは応答で[アクセス制御-許可](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-341">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="2488f-342">`https://cors1.azurewebsites.net`このヘッダーの値は、要求のヘッダーと一致し `Origin` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-342">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="2488f-343"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>が呼び出されると、 `Access-Control-Allow-Origin: *` ワイルドカード値が返されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-343">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="2488f-344">`AllowAnyOrigin`任意の発信元を許可します。</span><span class="sxs-lookup"><span data-stu-id="2488f-344">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="2488f-345">応答にヘッダーが含まれていない場合 `Access-Control-Allow-Origin` 、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="2488f-345">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="2488f-346">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-346">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="2488f-347">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="2488f-347">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="2488f-348">表示オプションの要求</span><span class="sxs-lookup"><span data-stu-id="2488f-348">Display OPTIONS requests</span></span>

<span data-ttu-id="2488f-349">既定では、Chrome および Edge ブラウザーでは、F12 ツールの [ネットワーク] タブの [要求] オプションが表示されません。</span><span class="sxs-lookup"><span data-stu-id="2488f-349">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="2488f-350">これらのブラウザーでオプションの要求を表示するには:</span><span class="sxs-lookup"><span data-stu-id="2488f-350">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="2488f-351">`chrome://flags/#out-of-blink-cors` または `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="2488f-351">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="2488f-352">フラグを無効にします。</span><span class="sxs-lookup"><span data-stu-id="2488f-352">disable the flag.</span></span>
* <span data-ttu-id="2488f-353">[再起動] をタップします。</span><span class="sxs-lookup"><span data-stu-id="2488f-353">restart.</span></span>

<span data-ttu-id="2488f-354">既定では、Firefox によってオプションの要求が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-354">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="2488f-355">IIS での CORS</span><span class="sxs-lookup"><span data-stu-id="2488f-355">CORS in IIS</span></span>

<span data-ttu-id="2488f-356">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合、CORS は Windows 認証の前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-356">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="2488f-357">このシナリオをサポートするには、アプリ用に[IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールして構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-357">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="2488f-358">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="2488f-358">Test CORS</span></span>

<span data-ttu-id="2488f-359">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)には、CORS をテストするコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2488f-359">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="2488f-360">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-360">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="2488f-361">このサンプルは、ページが追加された API プロジェクトです Razor 。</span><span class="sxs-lookup"><span data-stu-id="2488f-361">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="2488f-362">`WithOrigins("https://localhost:<port>");`サンプルアプリのテストにのみ使用してください[サンプルコードをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)します。</span><span class="sxs-lookup"><span data-stu-id="2488f-362">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="2488f-363">`ValuesController`テスト用のエンドポイントは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2488f-363">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="2488f-364">[Mydisplayrouteinfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)は、 [Rick](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet パッケージによって提供され、ルート情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="2488f-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="2488f-365">次のいずれかの方法を使用して、上記のサンプルコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="2488f-365">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="2488f-366">でデプロイされたサンプルアプリを使用し [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-366">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="2488f-367">サンプルをダウンロードする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2488f-367">There is no need to download the sample.</span></span>
* <span data-ttu-id="2488f-368">`dotnet run`の既定の URL を使用して、サンプルを実行し `https://localhost:5001` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-368">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="2488f-369">URL に対してポートを44398に設定して、Visual Studio からサンプルを実行し `https://localhost:44398` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-369">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="2488f-370">F12 ツールでブラウザーを使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2488f-370">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="2488f-371">[**値**] をクリックし、[**ネットワーク**] タブでヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="2488f-371">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="2488f-372">[**テストの配置**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2488f-372">Select the **PUT test** button.</span></span> <span data-ttu-id="2488f-373">OPTIONS 要求を表示する方法については、「[表示オプションの要求](#options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-373">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="2488f-374">**Put テスト**では、2つの要求、オプションのプレフライト要求、および put 要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-374">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="2488f-375">失敗し **`GetValues2 [DisableCors]`** た CORS 要求をトリガーするには、このボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2488f-375">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="2488f-376">ドキュメントに記載されているように、応答は200成功を返しますが、CORS 要求は行われません。</span><span class="sxs-lookup"><span data-stu-id="2488f-376">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="2488f-377">[**コンソール**] タブを選択して、CORS エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="2488f-377">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="2488f-378">ブラウザーによっては、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-378">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="2488f-379">`'https://cors1.azurewebsites.net/api/values/GetValues2'`送信元からのフェッチへのアクセス `'https://cors3.azurewebsites.net'` が CORS ポリシーによってブロックされました。要求されたリソースに ' アクセス制御許可-発信元 ' ヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-379">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="2488f-380">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="2488f-380">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="2488f-381">CORS が有効なエンドポイントは、 [curl](https://curl.haxx.se/)、 [Fiddler](https://www.telerik.com/fiddler)、 [Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="2488f-381">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="2488f-382">ツールを使用する場合、ヘッダーによって指定された要求の配信元は、要求を受信している `Origin` ホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-382">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="2488f-383">ヘッダーの値に基づいて要求が*クロスオリジン*でない場合は、 `Origin` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2488f-383">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="2488f-384">CORS ミドルウェアが要求を処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2488f-384">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="2488f-385">CORS ヘッダーが応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="2488f-385">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="2488f-386">次のコマンドは、を使用し `curl` て、情報を含む OPTIONS 要求を発行します。</span><span class="sxs-lookup"><span data-stu-id="2488f-386">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="2488f-387">エンドポイントルーティングを使用した CORS のテスト [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="2488f-387">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="2488f-388">現在、を使用して、エンドポイントごとに CORS を有効にする `RequireCors` ことは、[自動プレフライト要求](#apf)をサポートして**いません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-388">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="2488f-389">[エンドポイントルーティングを使用して CORS を有効に](#ecors)する次のコードについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="2488f-389">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="2488f-390">`TodoItems1Controller`テスト用のエンドポイントは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2488f-390">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="2488f-391">配置された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[テストページ](https://cors1.azurewebsites.net/test?number=1)から、前のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="2488f-391">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="2488f-392">エンドポイントがプレフライト要求に応答して応答するため、 **Delete [EnableCors** **] ボタンは**正常に終了し `[EnableCors]` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-392">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="2488f-393">他のエンドポイントは失敗します。</span><span class="sxs-lookup"><span data-stu-id="2488f-393">The other endpoints fails.</span></span> <span data-ttu-id="2488f-394">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)によってが送信されるため、 **GET**ボタンは失敗します。</span><span class="sxs-lookup"><span data-stu-id="2488f-394">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="2488f-395">次の例は `TodoItems2Controller` 同様のエンドポイントを提供しますが、オプションの要求に応答するための明示的なコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2488f-395">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="2488f-396">配置されたサンプルの[テストページ](https://cors1.azurewebsites.net/test?number=2)から、前のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="2488f-396">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="2488f-397">[**コントローラー** ] ドロップダウンリストで、[**プレフライト**] を選択し、[**コントローラーの設定**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2488f-397">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="2488f-398">エンドポイントに対するすべての CORS 呼び出しが `TodoItems2Controller` 成功します。</span><span class="sxs-lookup"><span data-stu-id="2488f-398">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2488f-399">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2488f-399">Additional resources</span></span>

* [<span data-ttu-id="2488f-400">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="2488f-400">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="2488f-401">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="2488f-401">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2488f-402">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2488f-402">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2488f-403">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-403">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="2488f-404">ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="2488f-404">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="2488f-405">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-405">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="2488f-406">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="2488f-406">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="2488f-407">場合によっては、他のサイトがアプリに対してクロスオリジン要求を行うことを許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-407">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="2488f-408">詳細については、「 [MOZILLA CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-408">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="2488f-409">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="2488f-409">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="2488f-410">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="2488f-410">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="2488f-411">は、CORS 緩和され security のセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-411">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="2488f-412">CORS を許可すると、API の安全性が向上しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-412">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="2488f-413">詳細については、「 [CORS のしくみ](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-413">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="2488f-414">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="2488f-414">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="2488f-415">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="2488f-415">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="2488f-416">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2488f-416">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="2488f-417">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="2488f-417">Same origin</span></span>

<span data-ttu-id="2488f-418">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="2488f-418">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="2488f-419">この2つの Url のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="2488f-419">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="2488f-420">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="2488f-420">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="2488f-421">`https://example.net`: 異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="2488f-421">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="2488f-422">`https://www.example.com/foo.html`: 異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="2488f-422">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="2488f-423">`http://example.com/foo.html`: 異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="2488f-423">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="2488f-424">`https://example.com:9000/foo.html`: 別のポート</span><span class="sxs-lookup"><span data-stu-id="2488f-424">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="2488f-425">Internet Explorer は、オリジンを比較するときにポートを考慮しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-425">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="2488f-426">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="2488f-426">CORS with named policy and middleware</span></span>

<span data-ttu-id="2488f-427">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="2488f-427">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="2488f-428">次のコードでは、指定したオリジンのアプリ全体に対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="2488f-428">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="2488f-429">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2488f-429">The preceding code:</span></span>

* <span data-ttu-id="2488f-430">ポリシー名を " \_ myallow固有のオリジン" に設定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-430">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="2488f-431">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="2488f-431">The policy name is arbitrary.</span></span>
* <span data-ttu-id="2488f-432"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>CORS を有効にする拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2488f-432">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="2488f-433"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2488f-433">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="2488f-434">ラムダはオブジェクトを受け取り <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-434">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="2488f-435">などの[構成オプション](#cors-policy-options)に `WithOrigins` ついては、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-435">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="2488f-436">メソッド呼び出しによって、 <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> アプリのサービスコンテナーに CORS サービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-436">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="2488f-437">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-437">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="2488f-438">メソッドは、 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 次のコードに示すようにメソッドを連結できます。</span><span class="sxs-lookup"><span data-stu-id="2488f-438">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="2488f-439">注: URL の末尾にスラッシュ () を含めることは**できません** `/` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-439">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="2488f-440">URL がで終了した場合 `/` 、比較はを返し、 `false` ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="2488f-440">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="2488f-441">次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-441">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="2488f-442">メモ: `UseCors` の前にを呼び出す必要があり `UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-442">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="2488f-443">ページ/コントローラー/アクションレベルで CORS ポリシーを適用するには[ Razor 、「ページ、コントローラー、およびアクションメソッドで Cors を有効](#ecors)にする」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-443">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="2488f-444">前のコードのようなコードをテストする方法については、「[テスト CORS](#test) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-444">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="2488f-445">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="2488f-445">Enable CORS with attributes</span></span>

<span data-ttu-id="2488f-446">[ &lbrack; Enablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用するための代替手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="2488f-446">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="2488f-447">属性を指定すると `[EnableCors]` 、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS が有効になります。</span><span class="sxs-lookup"><span data-stu-id="2488f-447">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="2488f-448">を使用し `[EnableCors]` て、既定のポリシーを指定し、 `[EnableCors("{Policy String}")]` ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-448">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="2488f-449">`[EnableCors]`属性は次のものに適用できます。</span><span class="sxs-lookup"><span data-stu-id="2488f-449">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="2488f-450">改`PageModel`</span><span class="sxs-lookup"><span data-stu-id="2488f-450"> Page `PageModel`</span></span>
* <span data-ttu-id="2488f-451">コントローラー</span><span class="sxs-lookup"><span data-stu-id="2488f-451">Controller</span></span>
* <span data-ttu-id="2488f-452">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="2488f-452">Controller action method</span></span>

<span data-ttu-id="2488f-453">属性を使用して、コントローラー/ページモデル/アクションに異なるポリシーを適用でき `[EnableCors]` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-453">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="2488f-454">`[EnableCors]`属性がコントローラー/ページモデル/アクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、**両方**のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-454">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="2488f-455">ポリシーを組み合わせることはお勧めし**ません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-455">We recommend **not** combining policies.</span></span> <span data-ttu-id="2488f-456">`[EnableCors]`両方では**なく**、属性またはミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-456">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="2488f-457">を使用する場合は `[EnableCors]` 、既定のポリシーを定義**しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="2488f-457">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="2488f-458">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-458">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="2488f-459">次のコードでは、CORS の既定のポリシーとという名前のポリシーを作成し `"AnotherPolicy"` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-459">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="2488f-460">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="2488f-460">Disable CORS</span></span>

<span data-ttu-id="2488f-461">[ &lbrack; Disablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラー/ページモデル/アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2488f-461">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="2488f-462">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="2488f-462">CORS policy options</span></span>

<span data-ttu-id="2488f-463">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-463">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="2488f-464">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="2488f-464">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="2488f-465">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-465">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="2488f-466">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-466">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="2488f-467">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-467">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="2488f-468">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="2488f-468">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="2488f-469">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-469">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="2488f-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>はで呼び出され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2488f-471">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-471">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="2488f-472">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="2488f-472">Set the allowed origins</span></span>

<span data-ttu-id="2488f-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: 任意のスキーム (または) を持つすべてのオリジンからの CORS 要求を許可 `http` `https` します。</span><span class="sxs-lookup"><span data-stu-id="2488f-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="2488f-474">`AllowAnyOrigin`*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="2488f-474">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="2488f-475">とを指定すると、 `AllowAnyOrigin` `AllowCredentials` 安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-475">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="2488f-476">セキュリティで保護されたアプリの場合は、クライアントがサーバーリソースへのアクセスを承認する必要がある場合は、オリジンの正確な一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-476">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="2488f-477">`AllowAnyOrigin`プレフライト要求とヘッダーに影響し `Access-Control-Allow-Origin` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-477">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="2488f-478">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="2488f-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインとオリジンが一致するようにするための関数として、ポリシーのプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="2488f-480">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-480">Set the allowed HTTP methods</span></span>

<span data-ttu-id="2488f-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="2488f-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="2488f-482">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="2488f-482">Allows any HTTP method:</span></span>
* <span data-ttu-id="2488f-483">プレフライト要求とヘッダーに影響し `Access-Control-Allow-Methods` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-483">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="2488f-484">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-484">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="2488f-485">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-485">Set the allowed request headers</span></span>

<span data-ttu-id="2488f-486">CORS 要求で特定のヘッダーを送信できるようにするには、 *author 要求ヘッダー*と呼ばれるを呼び出し、 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-486">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="2488f-487">すべての作成者要求ヘッダーを許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-487">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="2488f-488">この設定は、プレフライト要求とヘッダーに影響し `Access-Control-Request-Headers` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-488">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="2488f-489">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-489">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="2488f-490">CORS ミドルウェアでは、 `Access-Control-Request-Headers` CorsPolicy で構成されている値に関係なく、常にの4つのヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="2488f-490">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="2488f-491">このヘッダーの一覧には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-491">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="2488f-492">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="2488f-492">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="2488f-493">は常にホワイトリストに登録されているため、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求に正常に応答し `Content-Language` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-493">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="2488f-494">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-494">Set the exposed response headers</span></span>

<span data-ttu-id="2488f-495">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-495">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="2488f-496">詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-496">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="2488f-497">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2488f-497">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="2488f-498">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2488f-498">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="2488f-499">アプリで他のヘッダーを使用できるようにするには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-499">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="2488f-500">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="2488f-500">Credentials in cross-origin requests</span></span>

<span data-ttu-id="2488f-501">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-501">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="2488f-502">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-502">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="2488f-503">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-503">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="2488f-504">クロスオリジン要求で資格情報を送信するには、クライアントがに設定されている必要があり `XMLHttpRequest.withCredentials` `true` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-504">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="2488f-505">直接の使用 `XMLHttpRequest` :</span><span class="sxs-lookup"><span data-stu-id="2488f-505">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="2488f-506">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="2488f-506">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="2488f-507">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="2488f-507">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="2488f-508">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-508">The server must allow the credentials.</span></span> <span data-ttu-id="2488f-509">クロスオリジンの資格情報を許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-509">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="2488f-510">HTTP 応答には、 `Access-Control-Allow-Credentials` サーバーがクロスオリジン要求に対して資格情報を許可することをブラウザーに示すヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2488f-510">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="2488f-511">ブラウザーが資格情報を送信しても、応答に有効なヘッダーが含まれていない場合 `Access-Control-Allow-Credentials` 、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="2488f-511">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="2488f-512">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="2488f-512">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="2488f-513">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="2488f-513">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="2488f-514">また、CORS 仕様では、ヘッダーが存在する場合、[オリジン] を [(すべてのオリジン)] に設定することもでき `"*"` `Access-Control-Allow-Credentials` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-514">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="2488f-515">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="2488f-515">Preflight requests</span></span>

<span data-ttu-id="2488f-516">一部の CORS 要求については、ブラウザーは実際の要求を行う前に追加の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="2488f-516">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="2488f-517">この要求は*プレフライト要求*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-517">This request is called a *preflight request*.</span></span> <span data-ttu-id="2488f-518">次の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="2488f-518">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="2488f-519">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="2488f-519">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="2488f-520">アプリで `Accept` は、、、 `Accept-Language` `Content-Language` 、 `Content-Type` 、または `Last-Event-ID` 以外の要求ヘッダーは設定されません。</span><span class="sxs-lookup"><span data-stu-id="2488f-520">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="2488f-521">ヘッダーには、設定されて `Content-Type` いる場合、次のいずれかの値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-521">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="2488f-522">クライアント要求に対して設定された要求ヘッダーに適用される規則は、オブジェクトに対してを呼び出すことによって、アプリが設定するヘッダーに適用され `setRequestHeader` `XMLHttpRequest` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-522">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="2488f-523">CORS 仕様は、これらのヘッダー*作成者要求ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2488f-523">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="2488f-524">このルールは、ブラウザーが設定できるヘッダー (、、など) には適用されません `User-Agent` `Host` `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-524">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="2488f-525">プレフライト要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="2488f-525">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="2488f-526">事前フライト要求では、HTTP OPTIONS メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-526">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="2488f-527">次の2つの特殊なヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2488f-527">It includes two special headers:</span></span>

* <span data-ttu-id="2488f-528">`Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="2488f-528">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="2488f-529">`Access-Control-Request-Headers`: アプリが実際の要求に対して設定する要求ヘッダーのリスト。</span><span class="sxs-lookup"><span data-stu-id="2488f-529">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="2488f-530">前述のように、これにはブラウザーが設定するヘッダー (など) は含まれません `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-530">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="2488f-531">CORS が適切なポリシーで有効になっている場合、ASP.NET Core は通常、CORS プレフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="2488f-531">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="2488f-532">[プレフライト要求の場合は、[HttpOptions] 属性を](#pro)参照してください。</span><span class="sxs-lookup"><span data-stu-id="2488f-532">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="2488f-533">CORS のプレフライト要求には、 `Access-Control-Request-Headers` 実際の要求と共に送信されるヘッダーをサーバーに示すヘッダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="2488f-533">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="2488f-534">特定のヘッダーを許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-534">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="2488f-535">すべての作成者要求ヘッダーを許可するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-535">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="2488f-536">ブラウザーの設定方法が完全に一致しているわけではありません `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="2488f-536">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="2488f-537">ヘッダーを以外の任意の値 (またはを使用) に設定する場合は、少なくとも、、、 `"*"` <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> `Accept` `Content-Type` `Origin` 、およびサポートするカスタムヘッダーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-537">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="2488f-538">プレフライト要求に対する応答の例を次に示します (サーバーが要求を許可していることを前提としています)。</span><span class="sxs-lookup"><span data-stu-id="2488f-538">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="2488f-539">応答には、 `Access-Control-Allow-Methods` 許可されているメソッドと、必要に応じてヘッダーを一覧表示するヘッダーが含まれてい `Access-Control-Allow-Headers` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-539">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="2488f-540">プレフライト要求が成功した場合、ブラウザーは実際の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="2488f-540">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="2488f-541">プレフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-541">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="2488f-542">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-542">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="2488f-543">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="2488f-543">Set the preflight expiration time</span></span>

<span data-ttu-id="2488f-544">ヘッダーは、 `Access-Control-Max-Age` プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="2488f-544">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="2488f-545">このヘッダーを設定するには、次のように呼び出し <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-545">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="2488f-546">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="2488f-546">How CORS works</span></span>

<span data-ttu-id="2488f-547">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="2488f-547">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="2488f-548">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="2488f-548">CORS is **not** a security feature.</span></span> <span data-ttu-id="2488f-549">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="2488f-549">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="2488f-550">たとえば、悪意のあるアクターがを使用してサイトに対して[クロスサイトスクリプティング (XSS) を防止](xref:security/cross-site-scripting)し、CORS が有効になっているサイトに対してクロスサイト要求を実行して情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="2488f-550">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="2488f-551">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="2488f-551">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="2488f-552">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="2488f-552">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="2488f-553">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="2488f-553">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="2488f-554">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-554">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="2488f-555">Fiddler</span><span class="sxs-lookup"><span data-stu-id="2488f-555">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="2488f-556">Postman</span><span class="sxs-lookup"><span data-stu-id="2488f-556">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="2488f-557">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="2488f-557">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="2488f-558">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="2488f-558">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="2488f-559">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="2488f-559">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="2488f-560">(CORS を使用しない) ブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="2488f-560">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="2488f-561">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="2488f-561">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="2488f-562">JSONP は XHR を使用しないので、タグを使用して `<script>` 応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="2488f-562">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="2488f-563">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="2488f-563">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="2488f-564">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="2488f-564">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="2488f-565">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-565">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="2488f-566">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2488f-566">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="2488f-567">次に、クロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="2488f-567">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="2488f-568">ヘッダーは、 `Origin` 要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="2488f-568">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="2488f-569">`Origin`ヘッダーは必須であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-569">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="2488f-570">サーバーが要求を許可している場合は、応答でヘッダーが設定され `Access-Control-Allow-Origin` ます。</span><span class="sxs-lookup"><span data-stu-id="2488f-570">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="2488f-571">このヘッダーの値は、要求のヘッダーと一致するか、 `Origin` またはワイルドカード値です `"*"` 。つまり、すべての配信元が許可されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-571">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="2488f-572">応答にヘッダーが含まれていない場合 `Access-Control-Allow-Origin` 、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="2488f-572">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="2488f-573">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="2488f-573">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="2488f-574">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="2488f-574">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="2488f-575">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="2488f-575">Test CORS</span></span>

<span data-ttu-id="2488f-576">CORS をテストするには:</span><span class="sxs-lookup"><span data-stu-id="2488f-576">To test CORS:</span></span>

1. <span data-ttu-id="2488f-577">[API プロジェクトを作成](xref:tutorials/first-web-api)します。</span><span class="sxs-lookup"><span data-stu-id="2488f-577">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="2488f-578">または、[サンプルをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。</span><span class="sxs-lookup"><span data-stu-id="2488f-578">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="2488f-579">このドキュメントのいずれかの方法を使用して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="2488f-579">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="2488f-580">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2488f-580">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="2488f-581">`WithOrigins("https://localhost:<port>");`サンプルアプリのテストにのみ使用してください[サンプルコードをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)します。</span><span class="sxs-lookup"><span data-stu-id="2488f-581">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="2488f-582">Web アプリプロジェクトを作成 Razor します (ページまたは MVC)。</span><span class="sxs-lookup"><span data-stu-id="2488f-582">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="2488f-583">このサンプルでは、 Razor ページを使用します。</span><span class="sxs-lookup"><span data-stu-id="2488f-583">The sample uses Razor Pages.</span></span> <span data-ttu-id="2488f-584">API プロジェクトと同じソリューションに web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2488f-584">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="2488f-585">次の強調表示されたコードを*インデックスの cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2488f-585">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="2488f-586">前のコードで、をデプロイされた `url: 'https://<web app>.azurewebsites.net/api/values/1',` アプリの URL に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2488f-586">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="2488f-587">API プロジェクトをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="2488f-587">Deploy the API project.</span></span> <span data-ttu-id="2488f-588">たとえば、 [Azure にデプロイ](xref:host-and-deploy/azure-apps/index)します。</span><span class="sxs-lookup"><span data-stu-id="2488f-588">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="2488f-589">Razorデスクトップからページまたは MVC アプリを実行し、[**テスト**] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2488f-589">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="2488f-590">F12 ツールを使用して、エラーメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="2488f-590">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="2488f-591">から localhost の配信元を削除 `WithOrigins` し、アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="2488f-591">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="2488f-592">または、別のポートを使用してクライアントアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2488f-592">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="2488f-593">たとえば、Visual Studio からを実行します。</span><span class="sxs-lookup"><span data-stu-id="2488f-593">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="2488f-594">クライアントアプリでテストします。</span><span class="sxs-lookup"><span data-stu-id="2488f-594">Test with the client app.</span></span> <span data-ttu-id="2488f-595">CORS エラーはエラーを返しますが、エラーメッセージは JavaScript では使用できません。</span><span class="sxs-lookup"><span data-stu-id="2488f-595">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="2488f-596">F12 ツールの [コンソール] タブを使用して、エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="2488f-596">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="2488f-597">ブラウザーによっては、次のようなエラー (F12 ツールコンソール) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2488f-597">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="2488f-598">Microsoft Edge を使用する:</span><span class="sxs-lookup"><span data-stu-id="2488f-598">Using Microsoft Edge:</span></span>

     <span data-ttu-id="2488f-599">**SEC7120: [CORS] オリジンは、次の `https://localhost:44375` `https://localhost:44375` 場所にあるクロスオリジンリソースのアクセス制御-許可-オリジン応答ヘッダーで見つかりませんでした`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="2488f-599">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="2488f-600">Chrome を使用する:</span><span class="sxs-lookup"><span data-stu-id="2488f-600">Using Chrome:</span></span>

     <span data-ttu-id="2488f-601">**`https://webapi.azurewebsites.net/api/values/1`オリジンからの XMLHttpRequest へのアクセス `https://localhost:44375` が CORS ポリシーによってブロックされています。要求されたリソースに ' アクセス制御許可-発信元 ' ヘッダーが存在しません。**</span><span class="sxs-lookup"><span data-stu-id="2488f-601">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="2488f-602">CORS が有効なエンドポイントは、 [Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="2488f-602">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="2488f-603">ツールを使用する場合、ヘッダーによって指定された要求の配信元は、要求を受信している `Origin` ホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-603">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="2488f-604">ヘッダーの値に基づいて要求が*クロスオリジン*でない場合は、 `Origin` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2488f-604">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="2488f-605">CORS ミドルウェアが要求を処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2488f-605">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="2488f-606">CORS ヘッダーが応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="2488f-606">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="2488f-607">IIS での CORS</span><span class="sxs-lookup"><span data-stu-id="2488f-607">CORS in IIS</span></span>

<span data-ttu-id="2488f-608">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合、CORS は Windows 認証の前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-608">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="2488f-609">このシナリオをサポートするには、アプリ用に[IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールして構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2488f-609">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2488f-610">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2488f-610">Additional resources</span></span>

* [<span data-ttu-id="2488f-611">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="2488f-611">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="2488f-612">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="2488f-612">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
