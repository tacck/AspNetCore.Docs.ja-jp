---
<span data-ttu-id="0b999-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0b999-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0b999-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0b999-102">'Blazor'</span></span>
- <span data-ttu-id="0b999-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0b999-103">'Identity'</span></span>
- <span data-ttu-id="0b999-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0b999-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0b999-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0b999-105">'Razor'</span></span>
- <span data-ttu-id="0b999-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0b999-106">'SignalR' uid:</span></span> 

---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="0b999-107">カスタム ASP.NET Core ミドルウェアを記述する</span><span class="sxs-lookup"><span data-stu-id="0b999-107">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="0b999-108">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0b999-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0b999-109">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="0b999-109">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="0b999-110">ASP.NET Core からは、組み込みミドルウェア コンポーネントが豊富に提供されますが、カスタム ミドルウェアを記述したほうが良い場合もあります。</span><span class="sxs-lookup"><span data-stu-id="0b999-110">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="0b999-111">このトピックでは "*規約に基づく*" ミドルウェアを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0b999-111">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="0b999-112">厳密な型指定と要求ごとのアクティベーションを使用したアプローチについては、「<xref:fundamentals/middleware/extensibility>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b999-112">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="0b999-113">ミドルウェア クラス</span><span class="sxs-lookup"><span data-stu-id="0b999-113">Middleware class</span></span>

<span data-ttu-id="0b999-114">ミドルウェアは一般に、クラスにカプセル化され、拡張メソッドを使用して公開されます。</span><span class="sxs-lookup"><span data-stu-id="0b999-114">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="0b999-115">クエリ文字列から現在の要求のカルチャを設定する次のようなミドルウェアを考慮します。</span><span class="sxs-lookup"><span data-stu-id="0b999-115">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="0b999-116">上のサンプル コードを使って、ミドルウェア コンポーネントの作成方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0b999-116">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="0b999-117">ASP.NET Core に組み込まれているローカライズのサポートについては、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b999-117">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="0b999-118">カルチャを渡すことによって、ミドルウェアをテストします。</span><span class="sxs-lookup"><span data-stu-id="0b999-118">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="0b999-119">たとえば、`https://localhost:5001/?culture=no` を要求します。</span><span class="sxs-lookup"><span data-stu-id="0b999-119">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="0b999-120">次のコードは、ミドルウェアのデリゲートをクラスに移動します。</span><span class="sxs-lookup"><span data-stu-id="0b999-120">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="0b999-121">ミドルウェアのクラスには、次のものが含まれる必要があります。</span><span class="sxs-lookup"><span data-stu-id="0b999-121">The middleware class must include:</span></span>

* <span data-ttu-id="0b999-122"><xref:Microsoft.AspNetCore.Http.RequestDelegate> 型のパラメーターを持つパブリック コンストラクター。</span><span class="sxs-lookup"><span data-stu-id="0b999-122">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="0b999-123">`Invoke` または `InvokeAsync` という名前のパブリック メソッド。</span><span class="sxs-lookup"><span data-stu-id="0b999-123">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="0b999-124">このメソッドでは次のことが必要です。</span><span class="sxs-lookup"><span data-stu-id="0b999-124">This method must:</span></span>
  * <span data-ttu-id="0b999-125">`Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="0b999-125">Return a `Task`.</span></span>
  * <span data-ttu-id="0b999-126"><xref:Microsoft.AspNetCore.Http.HttpContext> 型の最初のパラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="0b999-126">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="0b999-127">コンストラクターおよび `Invoke`/`InvokeAsync` に対する追加のパラメーターは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) によって設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b999-127">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="0b999-128">ミドルウェアの依存関係</span><span class="sxs-lookup"><span data-stu-id="0b999-128">Middleware dependencies</span></span>

<span data-ttu-id="0b999-129">ミドルウェアは、コンストラクターで依存関係を公開することによって、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="0b999-129">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="0b999-130">ミドルウェアは、"*アプリケーションの有効期間*" ごとに 1 回構築されます。</span><span class="sxs-lookup"><span data-stu-id="0b999-130">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="0b999-131">要求内でミドルウェアとサービスを共有する必要がある場合は、「[要求ごとのミドルウェアの依存関係](#per-request-middleware-dependencies)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0b999-131">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="0b999-132">ミドルウェア コンポーネントは、コンストラクター パラメーターにより、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) から依存関係を解決できます。</span><span class="sxs-lookup"><span data-stu-id="0b999-132">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="0b999-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) は、追加パラメーターを直接受け入れることもできます。</span><span class="sxs-lookup"><span data-stu-id="0b999-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="0b999-134">要求ごとのミドルウェアの依存関係</span><span class="sxs-lookup"><span data-stu-id="0b999-134">Per-request middleware dependencies</span></span>

<span data-ttu-id="0b999-135">ミドルウェアは要求ごとではなくアプリの起動時に構築されるため、ミドルウェアのコンストラクターによって使われる "*スコープ*" 有効期間のサービスは、各要求の間に、依存関係によって挿入される他の種類と共有されません。</span><span class="sxs-lookup"><span data-stu-id="0b999-135">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="0b999-136">ミドルウェアとその他の種類の間で "*スコープ*" サービスを共有する必要がある場合は、これらのサービスを `Invoke` メソッドのシグネチャに追加します。</span><span class="sxs-lookup"><span data-stu-id="0b999-136">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="0b999-137">`Invoke` メソッドは、DI によって設定される追加のパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="0b999-137">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="0b999-138">ミドルウェア拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="0b999-138">Middleware extension method</span></span>

<span data-ttu-id="0b999-139">次の拡張メソッドは、<xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> を介してミドルウェアを公開します。</span><span class="sxs-lookup"><span data-stu-id="0b999-139">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="0b999-140">次のコードは、`Startup.Configure` からミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0b999-140">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="0b999-141">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0b999-141">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
