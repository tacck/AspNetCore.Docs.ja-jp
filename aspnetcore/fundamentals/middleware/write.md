---
title: カスタム ASP.NET Core ミドルウェアを記述する
author: rick-anderson
description: カスタム ASP.NET Core ミドルウェアを記述する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: fundamentals/middleware/write
ms.openlocfilehash: 5f33691cbcc00f407fff907ca62547fd80f2aa3c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057467"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="2a4b4-103">カスタム ASP.NET Core ミドルウェアを記述する</span><span class="sxs-lookup"><span data-stu-id="2a4b4-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="2a4b4-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2a4b4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2a4b4-105">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="2a4b4-106">ASP.NET Core からは、組み込みミドルウェア コンポーネントが豊富に提供されますが、カスタム ミドルウェアを記述したほうが良い場合もあります。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="2a4b4-107">このトピックでは " *規約に基づく* " ミドルウェアを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-107">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="2a4b4-108">厳密な型指定と要求ごとのアクティベーションを使用したアプローチについては、「<xref:fundamentals/middleware/extensibility>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-108">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="2a4b4-109">ミドルウェア クラス</span><span class="sxs-lookup"><span data-stu-id="2a4b4-109">Middleware class</span></span>

<span data-ttu-id="2a4b4-110">ミドルウェアは一般に、クラスにカプセル化され、拡張メソッドを使用して公開されます。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-110">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="2a4b4-111">クエリ文字列から現在の要求のカルチャを設定する次のようなミドルウェアを考慮します。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-111">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="2a4b4-112">上のサンプル コードを使って、ミドルウェア コンポーネントの作成方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-112">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="2a4b4-113">ASP.NET Core に組み込まれているローカライズのサポートについては、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-113">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="2a4b4-114">カルチャを渡すことによって、ミドルウェアをテストします。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-114">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="2a4b4-115">たとえば、`https://localhost:5001/?culture=no` を要求します。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-115">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="2a4b4-116">次のコードは、ミドルウェアのデリゲートをクラスに移動します。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-116">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="2a4b4-117">ミドルウェアのクラスには、次のものが含まれる必要があります。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-117">The middleware class must include:</span></span>

* <span data-ttu-id="2a4b4-118"><xref:Microsoft.AspNetCore.Http.RequestDelegate> 型のパラメーターを持つパブリック コンストラクター。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-118">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="2a4b4-119">`Invoke` または `InvokeAsync` という名前のパブリック メソッド。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-119">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="2a4b4-120">このメソッドでは次のことが必要です。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-120">This method must:</span></span>
  * <span data-ttu-id="2a4b4-121">`Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-121">Return a `Task`.</span></span>
  * <span data-ttu-id="2a4b4-122"><xref:Microsoft.AspNetCore.Http.HttpContext> 型の最初のパラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-122">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="2a4b4-123">コンストラクターおよび `Invoke`/`InvokeAsync` に対する追加のパラメーターは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) によって設定されます。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-123">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="2a4b4-124">ミドルウェアの依存関係</span><span class="sxs-lookup"><span data-stu-id="2a4b4-124">Middleware dependencies</span></span>

<span data-ttu-id="2a4b4-125">ミドルウェアは、コンストラクターで依存関係を公開することによって、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-125">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="2a4b4-126">ミドルウェアは、" *アプリケーションの有効期間* " ごとに 1 回構築されます。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-126">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="2a4b4-127">要求内でミドルウェアとサービスを共有する必要がある場合は、「[要求ごとのミドルウェアの依存関係](#per-request-middleware-dependencies)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-127">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="2a4b4-128">ミドルウェア コンポーネントは、コンストラクター パラメーターにより、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) から依存関係を解決できます。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-128">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="2a4b4-129">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) は、追加パラメーターを直接受け入れることもできます。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-129">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="2a4b4-130">要求ごとのミドルウェアの依存関係</span><span class="sxs-lookup"><span data-stu-id="2a4b4-130">Per-request middleware dependencies</span></span>

<span data-ttu-id="2a4b4-131">ミドルウェアは要求ごとではなくアプリの起動時に構築されるため、ミドルウェアのコンストラクターによって使われる " *スコープ* " 有効期間のサービスは、各要求の間に、依存関係によって挿入される他の種類と共有されません。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-131">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="2a4b4-132">ミドルウェアとその他の種類の間で " *スコープ* " サービスを共有する必要がある場合は、これらのサービスを `Invoke` メソッドのシグネチャに追加します。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-132">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="2a4b4-133">`Invoke` メソッドは、DI によって設定される追加のパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-133">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

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

<span data-ttu-id="2a4b4-134">[有効期間と登録のオプション](xref:fundamentals/dependency-injection#lifetime-and-registration-options)には、" *スコープ付き* " 有効期間サービスを含むミドルウェアの完全なサンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-134">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* lifetime services.</span></span>

## <a name="middleware-extension-method"></a><span data-ttu-id="2a4b4-135">ミドルウェア拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="2a4b4-135">Middleware extension method</span></span>

<span data-ttu-id="2a4b4-136">次の拡張メソッドは、<xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> を介してミドルウェアを公開します。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-136">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="2a4b4-137">次のコードは、`Startup.Configure` からミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-137">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="2a4b4-138">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2a4b4-138">Additional resources</span></span>

* <span data-ttu-id="2a4b4-139">[有効期間と登録のオプション](xref:fundamentals/dependency-injection#lifetime-and-registration-options)には、 *スコープ* 、 *一時* 、 *シングルトン* 有効期間サービスを含むミドルウェアの完全なサンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2a4b4-139">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* , *transient* , and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
