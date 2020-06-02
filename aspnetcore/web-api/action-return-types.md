---
<span data-ttu-id="74f79-101">title: ASP.NET Core web API author でのコントローラーアクションの戻り値の型: scottaddie description: ASP.NET Core web API でのさまざまなコントローラーアクションメソッドの戻り値の型の使用について説明します。</span><span class="sxs-lookup"><span data-stu-id="74f79-101">title: Controller action return types in ASP.NET Core web API author: scottaddie description: Learn about using the various controller action method return types in an ASP.NET Core web API.</span></span>
<span data-ttu-id="74f79-102">ms. author: scaddie ms. カスタム: mvc ms. date: 02/03/2020 no loc:</span><span class="sxs-lookup"><span data-stu-id="74f79-102">ms.author: scaddie ms.custom: mvc ms.date: 02/03/2020 no-loc:</span></span>
- <span data-ttu-id="74f79-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74f79-103">'Blazor'</span></span>
- <span data-ttu-id="74f79-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74f79-104">'Identity'</span></span>
- <span data-ttu-id="74f79-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74f79-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="74f79-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74f79-106">'Razor'</span></span>
- <span data-ttu-id="74f79-107">' SignalR ' uid: web api/アクション-戻り値の型</span><span class="sxs-lookup"><span data-stu-id="74f79-107">'SignalR' uid: web-api/action-return-types</span></span>

---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="74f79-108">ASP.NET Core Web API のコントローラー アクションの戻り値の型</span><span class="sxs-lookup"><span data-stu-id="74f79-108">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="74f79-109">作成者: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="74f79-109">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="74f79-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="74f79-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="74f79-111">ASP.NET Core では、Web API コントローラー アクションの戻り値の型に次のオプションを提供しています。</span><span class="sxs-lookup"><span data-stu-id="74f79-111">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="74f79-112">特定の型</span><span class="sxs-lookup"><span data-stu-id="74f79-112">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="74f79-113">IActionResult</span><span class="sxs-lookup"><span data-stu-id="74f79-113">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="74f79-114">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="74f79-114">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="74f79-115">特定の型</span><span class="sxs-lookup"><span data-stu-id="74f79-115">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="74f79-116">IActionResult</span><span class="sxs-lookup"><span data-stu-id="74f79-116">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="74f79-117">このドキュメントでは、各戻り値の型を使用するのが最適な場合について説明します。</span><span class="sxs-lookup"><span data-stu-id="74f79-117">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="74f79-118">特定の型</span><span class="sxs-lookup"><span data-stu-id="74f79-118">Specific type</span></span>

<span data-ttu-id="74f79-119">最もシンプルなアクションでは、プリミティブ データ型または複合データ型が返されます (`string` やカスタム オブジェクトの型など)。</span><span class="sxs-lookup"><span data-stu-id="74f79-119">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="74f79-120">カスタム `Product` オブジェクトのコレクションを返す次のアクションを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="74f79-120">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="74f79-121">アクションの実行中に保護できる既知の条件がない場合は、特定の型を返すだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="74f79-121">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="74f79-122">前のアクションでパラメーターを受け取っていないので、パラメーターの制約の検証は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="74f79-122">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="74f79-123">複数の戻り値の型が可能な場合は、戻り値の型 <xref:Microsoft.AspNetCore.Mvc.ActionResult> とプリミティブまたは複合の戻り値の型を混在させることが一般的です。</span><span class="sxs-lookup"><span data-stu-id="74f79-123">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="74f79-124">この種類のアクションに対応するには、 [Iactionresult](#iactionresult-type)または[ \<T> actionresult](#actionresultt-type)のいずれかが必要です。</span><span class="sxs-lookup"><span data-stu-id="74f79-124">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="74f79-125">このドキュメントでは、複数の戻り値の型のサンプルをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="74f79-125">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="74f79-126">IEnumerable \<T> または IAsyncEnumerable を返します\<T></span><span class="sxs-lookup"><span data-stu-id="74f79-126">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="74f79-127">ASP.NET Core 2.2 以前では、アクションから <xref:System.Collections.Generic.IEnumerable%601> が返されると、シリアライザーによって同期コレクションのイテレーションが行われます。</span><span class="sxs-lookup"><span data-stu-id="74f79-127">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="74f79-128">その結果、呼び出しがブロックされ、スレッド プールが枯渇する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="74f79-128">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="74f79-129">例として、Web API のデータ アクセスのニーズに Entity Framework (EF) コアが使用されているとします。</span><span class="sxs-lookup"><span data-stu-id="74f79-129">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="74f79-130">次のアクションの戻り値の型は、シリアル化中に同期的に列挙されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-130">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="74f79-131">ASP.NET Core 2.2 以前のデータベースで同期的な列挙とブロックの待機を回避するには、`ToListAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="74f79-131">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="74f79-132">ASP.NET Core 3.0 以降では、アクションから `IAsyncEnumerable<T>` を返します。</span><span class="sxs-lookup"><span data-stu-id="74f79-132">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="74f79-133">同期のイテレーションは行われなくなります。</span><span class="sxs-lookup"><span data-stu-id="74f79-133">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="74f79-134"><xref:System.Collections.Generic.IEnumerable%601> を返す場合と同様に効率的になります。</span><span class="sxs-lookup"><span data-stu-id="74f79-134">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="74f79-135">ASP.NET Core 3.0 以降では、次のアクションの結果がバッファーに出力されてから、シリアライザーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-135">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="74f79-136">非同期のイテレーションを保証するために、アクション シグネチャの戻り値の型を `IAsyncEnumerable<T>` と宣言することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="74f79-136">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="74f79-137">最終的に、イテレーション モードは、返される基となる具象型に基づいています。</span><span class="sxs-lookup"><span data-stu-id="74f79-137">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="74f79-138">MVC では、`IAsyncEnumerable<T>` を実装するすべての具象型が自動的にバッファーされます。</span><span class="sxs-lookup"><span data-stu-id="74f79-138">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="74f79-139">販売価格が付けられた製品レコードが `IEnumerable<Product>` として返される次のようなアクションを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="74f79-139">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="74f79-140">前の `IAsyncEnumerable<Product>` のアクションに相当するものは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="74f79-140">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="74f79-141">上記のアクションはいずれも ASP.NET Core 3.0 以降ではブロックされません。</span><span class="sxs-lookup"><span data-stu-id="74f79-141">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="74f79-142">IActionResult 型</span><span class="sxs-lookup"><span data-stu-id="74f79-142">IActionResult type</span></span>

<span data-ttu-id="74f79-143">戻り値の型 <xref:Microsoft.AspNetCore.Mvc.IActionResult> は、アクションの戻り値の型 `ActionResult` が複数考えられる場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="74f79-143">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="74f79-144">`ActionResult` 型は、さまざまな HTTP 状態コードを表します。</span><span class="sxs-lookup"><span data-stu-id="74f79-144">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="74f79-145">`ActionResult` から派生したすべての非抽象クラスは、有効な戻り値の型として修飾されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-145">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="74f79-146">このカテゴリの一般的な戻り値の型として、<xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400)、<xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404)、<xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200) などがあります。</span><span class="sxs-lookup"><span data-stu-id="74f79-146">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="74f79-147">また、<xref:Microsoft.AspNetCore.Mvc.ControllerBase> クラスの便利なメソッドを使用して、アクションから `ActionResult` 型を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="74f79-147">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="74f79-148">たとえば、`return BadRequest();` は、`return new BadRequestResult();` の短縮形です。</span><span class="sxs-lookup"><span data-stu-id="74f79-148">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="74f79-149">この種類のアクションには複数の戻り値の型とパスがあるため、属性を自由に使用する [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) 必要があります。</span><span class="sxs-lookup"><span data-stu-id="74f79-149">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="74f79-150">この属性は、[Swagger](xref:tutorials/web-api-help-pages-using-swagger) などのツールで生成される Web API ヘルプ ページのよりわかりやすい応答の詳細を生成します。</span><span class="sxs-lookup"><span data-stu-id="74f79-150">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="74f79-151">`[ProducesResponseType]` は、アクションによって返される既知の型と HTTP 状態コードを示します。</span><span class="sxs-lookup"><span data-stu-id="74f79-151">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="74f79-152">同期アクション</span><span class="sxs-lookup"><span data-stu-id="74f79-152">Synchronous action</span></span>

<span data-ttu-id="74f79-153">2 つの戻り値の型が考えられる、次の同期アクションを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="74f79-153">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="74f79-154">前のアクションの内容:</span><span class="sxs-lookup"><span data-stu-id="74f79-154">In the preceding action:</span></span>

* <span data-ttu-id="74f79-155">`id` によって表される製品が、基になるデータ ストアに存在しないと、404 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-155">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="74f79-156"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> の便利なメソッドは、`return new NotFoundResult();` の短縮形として呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-156">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="74f79-157">製品が存在する場合は、`Product` オブジェクトと共に 200 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-157">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="74f79-158"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> の便利なメソッドは、`return new OkObjectResult(product);` の短縮形として呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="74f79-159">非同期アクション</span><span class="sxs-lookup"><span data-stu-id="74f79-159">Asynchronous action</span></span>

<span data-ttu-id="74f79-160">2 つの戻り値の型が考えられる、次の非同期アクションを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="74f79-160">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="74f79-161">前のアクションの内容:</span><span class="sxs-lookup"><span data-stu-id="74f79-161">In the preceding action:</span></span>

* <span data-ttu-id="74f79-162">製品の説明に "XYZ Widget" が含まれている場合は、400 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-162">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="74f79-163"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> の便利なメソッドは、`return new BadRequestResult();` の短縮形として呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-163">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="74f79-164">201 状態コードは、製品の作成時に <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> の便利なメソッドによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-164">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="74f79-165">`CreatedAtAction` を呼び出す代替の方法として `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` があります。</span><span class="sxs-lookup"><span data-stu-id="74f79-165">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="74f79-166">このコード パスでは、`Product` オブジェクトは応答本文で指定されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-166">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="74f79-167">新しく作成された製品の URL を含む `Location` 応答ヘッダーが用意されています。</span><span class="sxs-lookup"><span data-stu-id="74f79-167">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="74f79-168">たとえば、次のモデルでは、要求に `Name` プロパティと `Description` プロパティを含める必要があることを示しています。</span><span class="sxs-lookup"><span data-stu-id="74f79-168">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="74f79-169">要求で `Name` と `Description` を提供するのに失敗すると、モデルの検証が失敗します。</span><span class="sxs-lookup"><span data-stu-id="74f79-169">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="74f79-170">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)ASP.NET Core 2.1 以降の属性が適用されている場合、モデル検証エラーは400状態コードになります。</span><span class="sxs-lookup"><span data-stu-id="74f79-170">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="74f79-171">詳細については、「[自動的な HTTP 400 応答](xref:web-api/index#automatic-http-400-responses)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="74f79-171">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="74f79-172">ActionResult \<T> 型</span><span class="sxs-lookup"><span data-stu-id="74f79-172">ActionResult\<T> type</span></span>

<span data-ttu-id="74f79-173">ASP.NET Core 2.1 では、web API コントローラーアクションの[Actionresult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1)戻り値の型が導入されました。</span><span class="sxs-lookup"><span data-stu-id="74f79-173">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="74f79-174">これで、<xref:Microsoft.AspNetCore.Mvc.ActionResult> から派生した型を返したり、[特定の型](#specific-type)を返したりすることができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="74f79-174">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="74f79-175">`ActionResult<T>` により、[IActionResult 型](#iactionresult-type)は次の利点を得られます。</span><span class="sxs-lookup"><span data-stu-id="74f79-175">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="74f79-176">[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)属性の `Type` プロパティは除外できます。</span><span class="sxs-lookup"><span data-stu-id="74f79-176">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="74f79-177">たとえば、`[ProducesResponseType(200, Type = typeof(Product))]` は `[ProducesResponseType(200)]` に簡略化されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-177">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="74f79-178">アクションの予期される戻り値の型は、代わりに `ActionResult<T>` の `T` から推論されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-178">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="74f79-179">[暗黙的なキャスト演算子](/dotnet/csharp/language-reference/keywords/implicit)は、`T` と `ActionResult` の両方の `ActionResult<T>` への変換をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="74f79-179">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="74f79-180">`T` は <xref:Microsoft.AspNetCore.Mvc.ObjectResult> に変換されます。つまり、`return new ObjectResult(T);` は `return T;` に簡略化されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-180">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="74f79-181">C# はインターフェイス上での暗黙的なキャスト演算子をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="74f79-181">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="74f79-182">そのため、`ActionResult<T>` を使用するには、インターフェイスを具象型に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74f79-182">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="74f79-183">たとえば、次の例における `IEnumerable` の使用は機能しません。</span><span class="sxs-lookup"><span data-stu-id="74f79-183">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="74f79-184">上記のコードを修正するための選択肢の 1 つは、`_repository.GetProducts().ToList();` を返すことです。</span><span class="sxs-lookup"><span data-stu-id="74f79-184">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="74f79-185">ほとんどのアクションには、特定の戻り値の型があります。</span><span class="sxs-lookup"><span data-stu-id="74f79-185">Most actions have a specific return type.</span></span> <span data-ttu-id="74f79-186">アクションの実行中に予期しない状態が発生する場合、特定の型は返されません。</span><span class="sxs-lookup"><span data-stu-id="74f79-186">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="74f79-187">たとえば、アクションの入力パラメーターがモデルの検証に失敗する場合があります。</span><span class="sxs-lookup"><span data-stu-id="74f79-187">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="74f79-188">このような場合、通常は特定の型ではなく、適切な `ActionResult` 型が返されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-188">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="74f79-189">同期アクション</span><span class="sxs-lookup"><span data-stu-id="74f79-189">Synchronous action</span></span>

<span data-ttu-id="74f79-190">2 つの戻り値の型が考えられる、同期アクションを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="74f79-190">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="74f79-191">前のアクションの内容:</span><span class="sxs-lookup"><span data-stu-id="74f79-191">In the preceding action:</span></span>

* <span data-ttu-id="74f79-192">製品がデータベース内に存在しない場合、404 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-192">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="74f79-193">製品が存在する場合、対応する `Product` オブジェクトと共に 200 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-193">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="74f79-194">ASP.NET Core 2.1 より前のバージョンは、`return product;` 行を `return Ok(product);` にする必要がありました。</span><span class="sxs-lookup"><span data-stu-id="74f79-194">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="74f79-195">非同期アクション</span><span class="sxs-lookup"><span data-stu-id="74f79-195">Asynchronous action</span></span>

<span data-ttu-id="74f79-196">2 つの戻り値の型が考えられる、非同期アクションを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="74f79-196">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="74f79-197">前のアクションの内容:</span><span class="sxs-lookup"><span data-stu-id="74f79-197">In the preceding action:</span></span>

* <span data-ttu-id="74f79-198">次の場合、ASP.NET Core ランタイムによって 400 状態コード (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) が返されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-198">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="74f79-199">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)属性が適用され、モデルの検証が失敗します。</span><span class="sxs-lookup"><span data-stu-id="74f79-199">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="74f79-200">製品の説明に "XYZ Widget" が含まれている。</span><span class="sxs-lookup"><span data-stu-id="74f79-200">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="74f79-201">201 状態コードは、製品の作成時に <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッドによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-201">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="74f79-202">このコード パスでは、`Product` オブジェクトは応答本文で指定されます。</span><span class="sxs-lookup"><span data-stu-id="74f79-202">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="74f79-203">新しく作成された製品の URL を含む `Location` 応答ヘッダーが用意されています。</span><span class="sxs-lookup"><span data-stu-id="74f79-203">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="74f79-204">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="74f79-204">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
