---
title: ASP.NET Core の ObjectPool を使用したオブジェクトの再利用
author: rick-anderson
description: ObjectPool を使用して ASP.NET Core アプリのパフォーマンスを向上させるためのヒントです。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: f29d15fc1e2d2ad84526598be14638110f08614e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774783"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="0e88e-103">ASP.NET Core の ObjectPool を使用したオブジェクトの再利用</span><span class="sxs-lookup"><span data-stu-id="0e88e-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="0e88e-104">([上田 Gordon](https://twitter.com/stevejgordon)、[ライアン Nowak](https://github.com/rynowak)、 [Rick Anderson](https://twitter.com/RickAndMSFT) )</span><span class="sxs-lookup"><span data-stu-id="0e88e-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0e88e-105"><xref:Microsoft.Extensions.ObjectPool>は、オブジェクトのガベージコレクションを許可するのではなく、オブジェクトのグループをメモリに保持して再利用できるようにする ASP.NET Core インフラストラクチャの一部です。</span><span class="sxs-lookup"><span data-stu-id="0e88e-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="0e88e-106">管理されているオブジェクトが次のような場合に、オブジェクトプールを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="0e88e-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="0e88e-107">割り当て/初期化にコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="0e88e-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="0e88e-108">限られたリソースを表します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-108">Represent some limited resource.</span></span>
- <span data-ttu-id="0e88e-109">予測と頻繁に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e88e-109">Used predictably and frequently.</span></span>

<span data-ttu-id="0e88e-110">たとえば、ASP.NET Core framework では、インスタンスを再利用<xref:System.Text.StringBuilder>するために、いくつかの場所でオブジェクトプールを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="0e88e-111">`StringBuilder`は、独自のバッファーを割り当てて管理し、文字データを保持します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="0e88e-112">ASP.NET Core が機能`StringBuilder`を実装するために定期的に使用し、それらを再利用することによって、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="0e88e-113">オブジェクトプーリングでは、常にパフォーマンスが向上するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="0e88e-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="0e88e-114">オブジェクトの初期化コストが高い場合を除き、通常は、プールからオブジェクトを取得する方が遅くなります。</span><span class="sxs-lookup"><span data-stu-id="0e88e-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="0e88e-115">プールによって管理されるオブジェクトは、プールが割り当て解除されるまで割り当てられません。</span><span class="sxs-lookup"><span data-stu-id="0e88e-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="0e88e-116">オブジェクトプールは、アプリまたはライブラリの現実的なシナリオを使用してパフォーマンスデータを収集した後にのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="0e88e-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="0e88e-117">**警告: は`ObjectPool`を実装`IDisposable`していません。破棄が必要な型では使用しないことをお勧めします。**</span><span class="sxs-lookup"><span data-stu-id="0e88e-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="0e88e-118">**注: ObjectPool では、割り当てられるオブジェクトの数に制限は設定されません。これにより、保持するオブジェクトの数に制限が適用されます。**</span><span class="sxs-lookup"><span data-stu-id="0e88e-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="0e88e-119">概念</span><span class="sxs-lookup"><span data-stu-id="0e88e-119">Concepts</span></span>

<span data-ttu-id="0e88e-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-基本的なオブジェクトプールの抽象化。</span><span class="sxs-lookup"><span data-stu-id="0e88e-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="0e88e-121">オブジェクトを取得して返すために使用します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-121">Used to get and return objects.</span></span>

<span data-ttu-id="0e88e-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-オブジェクトの作成方法と、オブジェクトがプールに返されるときの*リセット*方法をカスタマイズするには、これを実装します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="0e88e-123">これは、直接構築するオブジェクトプールに渡すことができます...もしくは</span><span class="sxs-lookup"><span data-stu-id="0e88e-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="0e88e-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>オブジェクトプールを作成するためのファクトリとして機能します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="0e88e-125">ObjectPool は、次のような複数の方法でアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="0e88e-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="0e88e-126">プールをインスタンス化しています。</span><span class="sxs-lookup"><span data-stu-id="0e88e-126">Instantiating a pool.</span></span>
* <span data-ttu-id="0e88e-127">[依存関係の挿入](xref:fundamentals/dependency-injection)(DI) にプールをインスタンスとして登録する。</span><span class="sxs-lookup"><span data-stu-id="0e88e-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="0e88e-128">を DI `ObjectPoolProvider<>`に登録し、ファクトリとして使用します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="0e88e-129">ObjectPool の使用方法</span><span class="sxs-lookup"><span data-stu-id="0e88e-129">How to use ObjectPool</span></span>

<span data-ttu-id="0e88e-130">オブジェクト<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>を取得し<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 、オブジェクトを返すには、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="0e88e-131">すべてのオブジェクトを返す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0e88e-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="0e88e-132">オブジェクトを返さない場合は、ガベージコレクションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e88e-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="0e88e-133">ObjectPool サンプル</span><span class="sxs-lookup"><span data-stu-id="0e88e-133">ObjectPool sample</span></span>

<span data-ttu-id="0e88e-134">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-134">The following code:</span></span>

* <span data-ttu-id="0e88e-135">`ObjectPoolProvider` [依存関係挿入](xref:fundamentals/dependency-injection)(DI) コンテナーにを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="0e88e-136">DI コンテナーに`ObjectPool<StringBuilder>`を追加して構成します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="0e88e-137">を`BirthdayMiddleware`追加します。</span><span class="sxs-lookup"><span data-stu-id="0e88e-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="0e88e-138">次のコードはを実装します。`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="0e88e-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
