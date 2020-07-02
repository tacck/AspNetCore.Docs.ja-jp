---
title: ASP.NET Core でのサードパーティ コンテナーによるミドルウェアのアクティブ化
author: rick-anderson
description: ASP.NET Core で、ファクトリベースのアクティブ化とサードパーティ コンテナーによる厳密に型指定されたミドルウェアを使用する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: a4224d62c11b4fee767c7b1c9b7d29f7e4f7d858
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407955"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="47569-103">ASP.NET Core でのサードパーティ コンテナーによるミドルウェアのアクティブ化</span><span class="sxs-lookup"><span data-stu-id="47569-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47569-104">この記事では、<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> と <xref:Microsoft.AspNetCore.Http.IMiddleware> を、サードパーティ コンテナーによる[ミドルウェア](xref:fundamentals/middleware/index)のアクティブ化の拡張ポイントとして使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="47569-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="47569-105">`IMiddlewareFactory` と `IMiddleware` の概要については、「<xref:fundamentals/middleware/extensibility>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="47569-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="47569-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="47569-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="47569-107">このサンプル アプリでは、`IMiddlewareFactory` の実装である `SimpleInjectorMiddlewareFactory` によるミドルウェアのアクティブ化を示します。</span><span class="sxs-lookup"><span data-stu-id="47569-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="47569-108">このサンプルでは、[Simple Injector](https://simpleinjector.org) 依存関係の挿入 (DI) コンテナーを使用しています。</span><span class="sxs-lookup"><span data-stu-id="47569-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="47569-109">サンプルのミドルウェアの実装は、クエリ文字列パラメーターで提供された値を記録します (`key`)。</span><span class="sxs-lookup"><span data-stu-id="47569-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="47569-110">ミドルウェアは、メモリ内データベースにクエリ文字列値を記録するのに、挿入されたデータベース コンテキスト (スコープ化されたサービス) を使用します。</span><span class="sxs-lookup"><span data-stu-id="47569-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="47569-111">このサンプル アプリでは、デモンストレーション目的でのみ [Simple Injector](https://github.com/simpleinjector/SimpleInjector) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="47569-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="47569-112">Simple Injector の使用を推奨するものではありません。</span><span class="sxs-lookup"><span data-stu-id="47569-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="47569-113">Simple Injector のドキュメントと GitHub Issues に記載されているミドルウェアのアクティブ化方法は、Simple Injector の保守担当者たちから推奨されています。</span><span class="sxs-lookup"><span data-stu-id="47569-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="47569-114">詳細については、[Simple Injector のドキュメント](https://simpleinjector.readthedocs.io/en/latest/index.html)と [Simple Injector の GitHub リポジトリ](https://github.com/simpleinjector/SimpleInjector)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="47569-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="47569-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="47569-115">IMiddlewareFactory</span></span>

<span data-ttu-id="47569-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> では、ミドルウェアを作成するメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="47569-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="47569-117">サンプル アプリでは、`SimpleInjectorActivatedMiddleware` インスタンスを作成するミドルウェア ファクトリが実装されています。</span><span class="sxs-lookup"><span data-stu-id="47569-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="47569-118">このミドルウェア ファクトリでは、Simple Injector コンテナーを使用してミドルウェアを解決しています。</span><span class="sxs-lookup"><span data-stu-id="47569-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="47569-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="47569-119">IMiddleware</span></span>

<span data-ttu-id="47569-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> では、アプリの要求パイプライン用にミドルウェアが定義されます。</span><span class="sxs-lookup"><span data-stu-id="47569-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="47569-121">`IMiddlewareFactory` の実装によってアクティブ化されるミドルウェア (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="47569-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="47569-122">ミドルウェア (*Middleware/MiddlewareExtensions.cs*) の拡張機能が作成されます。</span><span class="sxs-lookup"><span data-stu-id="47569-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="47569-123">`Startup.ConfigureServices` はいくつかのタスクを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="47569-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="47569-124">Simple Injector コンテナーを設定します。</span><span class="sxs-lookup"><span data-stu-id="47569-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="47569-125">ファクトリとミドルウェアを登録します。</span><span class="sxs-lookup"><span data-stu-id="47569-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="47569-126">Simple Injector コンテナーからアプリのデータベース コンテキストを利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="47569-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="47569-127">ミドルウェアは、要求を処理する `Startup.Configure` のパイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="47569-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="47569-128">この記事では、<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> と <xref:Microsoft.AspNetCore.Http.IMiddleware> を、サードパーティ コンテナーによる[ミドルウェア](xref:fundamentals/middleware/index)のアクティブ化の拡張ポイントとして使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="47569-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="47569-129">`IMiddlewareFactory` と `IMiddleware` の概要については、「<xref:fundamentals/middleware/extensibility>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="47569-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="47569-130">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="47569-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="47569-131">このサンプル アプリでは、`IMiddlewareFactory` の実装である `SimpleInjectorMiddlewareFactory` によるミドルウェアのアクティブ化を示します。</span><span class="sxs-lookup"><span data-stu-id="47569-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="47569-132">このサンプルでは、[Simple Injector](https://simpleinjector.org) 依存関係の挿入 (DI) コンテナーを使用しています。</span><span class="sxs-lookup"><span data-stu-id="47569-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="47569-133">サンプルのミドルウェアの実装は、クエリ文字列パラメーターで提供された値を記録します (`key`)。</span><span class="sxs-lookup"><span data-stu-id="47569-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="47569-134">ミドルウェアは、メモリ内データベースにクエリ文字列値を記録するのに、挿入されたデータベース コンテキスト (スコープ化されたサービス) を使用します。</span><span class="sxs-lookup"><span data-stu-id="47569-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="47569-135">このサンプル アプリでは、デモンストレーション目的でのみ [Simple Injector](https://github.com/simpleinjector/SimpleInjector) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="47569-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="47569-136">Simple Injector の使用を推奨するものではありません。</span><span class="sxs-lookup"><span data-stu-id="47569-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="47569-137">Simple Injector のドキュメントと GitHub Issues に記載されているミドルウェアのアクティブ化方法は、Simple Injector の保守担当者たちから推奨されています。</span><span class="sxs-lookup"><span data-stu-id="47569-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="47569-138">詳細については、[Simple Injector のドキュメント](https://simpleinjector.readthedocs.io/en/latest/index.html)と [Simple Injector の GitHub リポジトリ](https://github.com/simpleinjector/SimpleInjector)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="47569-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="47569-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="47569-139">IMiddlewareFactory</span></span>

<span data-ttu-id="47569-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> では、ミドルウェアを作成するメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="47569-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="47569-141">サンプル アプリでは、`SimpleInjectorActivatedMiddleware` インスタンスを作成するミドルウェア ファクトリが実装されています。</span><span class="sxs-lookup"><span data-stu-id="47569-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="47569-142">このミドルウェア ファクトリでは、Simple Injector コンテナーを使用してミドルウェアを解決しています。</span><span class="sxs-lookup"><span data-stu-id="47569-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="47569-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="47569-143">IMiddleware</span></span>

<span data-ttu-id="47569-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> では、アプリの要求パイプライン用にミドルウェアが定義されます。</span><span class="sxs-lookup"><span data-stu-id="47569-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="47569-145">`IMiddlewareFactory` の実装によってアクティブ化されるミドルウェア (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="47569-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="47569-146">ミドルウェア (*Middleware/MiddlewareExtensions.cs*) の拡張機能が作成されます。</span><span class="sxs-lookup"><span data-stu-id="47569-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="47569-147">`Startup.ConfigureServices` はいくつかのタスクを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="47569-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="47569-148">Simple Injector コンテナーを設定します。</span><span class="sxs-lookup"><span data-stu-id="47569-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="47569-149">ファクトリとミドルウェアを登録します。</span><span class="sxs-lookup"><span data-stu-id="47569-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="47569-150">Simple Injector コンテナーからアプリのデータベース コンテキストを利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="47569-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="47569-151">ミドルウェアは、要求を処理する `Startup.Configure` のパイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="47569-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="47569-152">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="47569-152">Additional resources</span></span>

* [<span data-ttu-id="47569-153">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="47569-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="47569-154">ファクトリ ベースのミドルウェアのアクティブ化</span><span class="sxs-lookup"><span data-stu-id="47569-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="47569-155">Simple Injector の GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="47569-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="47569-156">Simple Injector のドキュメント</span><span class="sxs-lookup"><span data-stu-id="47569-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
