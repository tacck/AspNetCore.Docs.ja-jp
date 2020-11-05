---
title: ASP.NET Core でのファクトリ ベースのミドルウェアのアクティブ化
author: rick-anderson
description: ASP.NET Core で、ファクトリ ベースの厳密に型指定されたミドルウェアをアクティブ化する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 125ebbba9fadf0db357fe7c69af7ca0d8ddc8041
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057493"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="4c6f2-103">ASP.NET Core でのファクトリ ベースのミドルウェアのアクティブ化</span><span class="sxs-lookup"><span data-stu-id="4c6f2-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4c6f2-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> は、[ミドルウェア](xref:fundamentals/middleware/index)のアクティブ化の拡張ポイントです。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="4c6f2-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 拡張メソッドでは、ミドルウェアの登録済みの型で <xref:Microsoft.AspNetCore.Http.IMiddleware> が実装されているかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="4c6f2-106">実装されている場合、規則に基づくミドルウェアのライセンス認証ロジックを使用する代わりに、コンテナーに登録されている <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> インスタンスが <xref:Microsoft.AspNetCore.Http.IMiddleware> の実装の解決に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="4c6f2-107">ミドルウェアは、アプリのサービス コンテナーで、[スコープ サービスまたは一時的サービス](xref:fundamentals/dependency-injection#service-lifetimes)として登録されています。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="4c6f2-108">利点:</span><span class="sxs-lookup"><span data-stu-id="4c6f2-108">Benefits:</span></span>

* <span data-ttu-id="4c6f2-109">クライアント要求ごとにアクティブ化 (スコープ サービスの挿入)</span><span class="sxs-lookup"><span data-stu-id="4c6f2-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="4c6f2-110">ミドルウェアの厳密な型指定</span><span class="sxs-lookup"><span data-stu-id="4c6f2-110">Strong typing of middleware</span></span>

<span data-ttu-id="4c6f2-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> はクライアント要求 (接続) ごとにアクティブ化されているので、スコープ サービスをミドルウェアのコンストラクターに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="4c6f2-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="4c6f2-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="4c6f2-113">IMiddleware</span></span>

<span data-ttu-id="4c6f2-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> では、アプリの要求パイプライン用にミドルウェアが定義されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="4c6f2-115">要求は、[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) メソッドによって処理され、ミドルウェアの実行を表す <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="4c6f2-116">規則でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="4c6f2-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="4c6f2-117"><xref:Microsoft.AspNetCore.Http.MiddlewareFactory> でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="4c6f2-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="4c6f2-118">ミドルウェア用に拡張機能が作成されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="4c6f2-119"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> では、ファクトリでアクティブ化されたミドルウェアにオブジェクトを渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="4c6f2-120">ファクトリでアクティブ化されたミドルウェアは、`Startup.ConfigureServices` の組み込みのコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4c6f2-121">この 2 つのミドルウェアは、要求を処理する `Startup.Configure` のパイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="4c6f2-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="4c6f2-122">IMiddlewareFactory</span></span>

<span data-ttu-id="4c6f2-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> では、ミドルウェアを作成するメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="4c6f2-124">ミドルウェア ファクトリの実装は、スコープ化されたサービスとして、コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="4c6f2-125">既定の <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> の実装である <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> は、[Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4c6f2-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> は、[ミドルウェア](xref:fundamentals/middleware/index)のアクティブ化の拡張ポイントです。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="4c6f2-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 拡張メソッドでは、ミドルウェアの登録済みの型で <xref:Microsoft.AspNetCore.Http.IMiddleware> が実装されているかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="4c6f2-128">実装されている場合、規則に基づくミドルウェアのライセンス認証ロジックを使用する代わりに、コンテナーに登録されている <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> インスタンスが <xref:Microsoft.AspNetCore.Http.IMiddleware> の実装の解決に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="4c6f2-129">ミドルウェアは、アプリのサービス コンテナーで、[スコープ サービスまたは一時的サービス](xref:fundamentals/dependency-injection#service-lifetimes)として登録されています。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="4c6f2-130">利点:</span><span class="sxs-lookup"><span data-stu-id="4c6f2-130">Benefits:</span></span>

* <span data-ttu-id="4c6f2-131">クライアント要求ごとにアクティブ化 (スコープ サービスの挿入)</span><span class="sxs-lookup"><span data-stu-id="4c6f2-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="4c6f2-132">ミドルウェアの厳密な型指定</span><span class="sxs-lookup"><span data-stu-id="4c6f2-132">Strong typing of middleware</span></span>

<span data-ttu-id="4c6f2-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> はクライアント要求 (接続) ごとにアクティブ化されているので、スコープ サービスをミドルウェアのコンストラクターに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="4c6f2-134">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="4c6f2-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="4c6f2-135">IMiddleware</span></span>

<span data-ttu-id="4c6f2-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> では、アプリの要求パイプライン用にミドルウェアが定義されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="4c6f2-137">要求は、[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) メソッドによって処理され、ミドルウェアの実行を表す <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="4c6f2-138">規則でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="4c6f2-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="4c6f2-139"><xref:Microsoft.AspNetCore.Http.MiddlewareFactory> でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="4c6f2-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="4c6f2-140">ミドルウェア用に拡張機能が作成されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="4c6f2-141"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> では、ファクトリでアクティブ化されたミドルウェアにオブジェクトを渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="4c6f2-142">ファクトリでアクティブ化されたミドルウェアは、`Startup.ConfigureServices` の組み込みのコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4c6f2-143">この 2 つのミドルウェアは、要求を処理する `Startup.Configure` のパイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="4c6f2-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="4c6f2-144">IMiddlewareFactory</span></span>

<span data-ttu-id="4c6f2-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> では、ミドルウェアを作成するメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="4c6f2-146">ミドルウェア ファクトリの実装は、スコープ化されたサービスとして、コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="4c6f2-147">既定の <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> の実装である <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> は、[Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="4c6f2-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4c6f2-148">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4c6f2-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
