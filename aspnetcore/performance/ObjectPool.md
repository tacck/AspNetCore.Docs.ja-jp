---
title: ASP.NET Core の ObjectPool を使用したオブジェクトの再利用
author: rick-anderson
description: ObjectPool を使用して ASP.NET Core アプリのパフォーマンスを向上させるためのヒントです。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 6997dbfdd5c654e4a8b15a026fd3ec61d024f02d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632370"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="8a07f-103">ASP.NET Core の ObjectPool を使用したオブジェクトの再利用</span><span class="sxs-lookup"><span data-stu-id="8a07f-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="8a07f-104">[上田 Gordon](https://twitter.com/stevejgordon)、[ライアン Nowak](https://github.com/rynowak)、および[Günther](https://github.com/gfoidl)があります。</span><span class="sxs-lookup"><span data-stu-id="8a07f-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="8a07f-105"><xref:Microsoft.Extensions.ObjectPool> は、オブジェクトのガベージコレクションを許可するのではなく、オブジェクトのグループをメモリに保持して再利用できるようにする ASP.NET Core インフラストラクチャの一部です。</span><span class="sxs-lookup"><span data-stu-id="8a07f-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="8a07f-106">管理されているオブジェクトが次のような場合に、オブジェクトプールを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="8a07f-107">割り当て/初期化にコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="8a07f-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="8a07f-108">限られたリソースを表します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-108">Represent some limited resource.</span></span>
- <span data-ttu-id="8a07f-109">予測と頻繁に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-109">Used predictably and frequently.</span></span>

<span data-ttu-id="8a07f-110">たとえば、ASP.NET Core framework では、インスタンスを再利用するために、いくつかの場所でオブジェクトプールを使用し <xref:System.Text.StringBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="8a07f-111">`StringBuilder` は、独自のバッファーを割り当てて管理し、文字データを保持します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="8a07f-112">ASP.NET Core `StringBuilder` が機能を実装するために定期的に使用し、それらを再利用することによって、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="8a07f-113">オブジェクトプーリングでは、常にパフォーマンスが向上するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="8a07f-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="8a07f-114">オブジェクトの初期化コストが高い場合を除き、通常は、プールからオブジェクトを取得する方が遅くなります。</span><span class="sxs-lookup"><span data-stu-id="8a07f-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="8a07f-115">プールによって管理されるオブジェクトは、プールが割り当て解除されるまで割り当てられません。</span><span class="sxs-lookup"><span data-stu-id="8a07f-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="8a07f-116">オブジェクトプールは、アプリまたはライブラリの現実的なシナリオを使用してパフォーマンスデータを収集した後にのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="8a07f-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="8a07f-117">**警告: はを `ObjectPool` 実装していません `IDisposable` 。破棄が必要な型では使用しないことをお勧めします。**</span><span class="sxs-lookup"><span data-stu-id="8a07f-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="8a07f-118">`ObjectPool` ASP.NET Core 3.0 以降では、をサポートして `IDisposable` います。</span><span class="sxs-lookup"><span data-stu-id="8a07f-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="8a07f-119">**注: ObjectPool では、割り当てられるオブジェクトの数に制限は設定されません。これにより、保持するオブジェクトの数に制限が適用されます。**</span><span class="sxs-lookup"><span data-stu-id="8a07f-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="8a07f-120">概念</span><span class="sxs-lookup"><span data-stu-id="8a07f-120">Concepts</span></span>

<span data-ttu-id="8a07f-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -基本的なオブジェクトプールの抽象化。</span><span class="sxs-lookup"><span data-stu-id="8a07f-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="8a07f-122">オブジェクトを取得して返すために使用します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-122">Used to get and return objects.</span></span>

<span data-ttu-id="8a07f-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -オブジェクトの作成方法と、オブジェクトがプールに返されるときの *リセット* 方法をカスタマイズするには、これを実装します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="8a07f-124">これは、直接構築するオブジェクトプールに渡すことができます...もしくは</span><span class="sxs-lookup"><span data-stu-id="8a07f-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="8a07f-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> オブジェクトプールを作成するためのファクトリとして機能します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="8a07f-126">ObjectPool は、次のような複数の方法でアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="8a07f-127">プールをインスタンス化しています。</span><span class="sxs-lookup"><span data-stu-id="8a07f-127">Instantiating a pool.</span></span>
* <span data-ttu-id="8a07f-128">[依存関係の挿入](xref:fundamentals/dependency-injection)(DI) にプールをインスタンスとして登録する。</span><span class="sxs-lookup"><span data-stu-id="8a07f-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="8a07f-129">を `ObjectPoolProvider<>` DI に登録し、ファクトリとして使用します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="8a07f-130">ObjectPool の使用方法</span><span class="sxs-lookup"><span data-stu-id="8a07f-130">How to use ObjectPool</span></span>

<span data-ttu-id="8a07f-131"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*>オブジェクトを取得し、オブジェクトを返すには、を呼び出し <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> ます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="8a07f-132">すべてのオブジェクトを返す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8a07f-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="8a07f-133">オブジェクトを返さない場合は、ガベージコレクションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="8a07f-134"><xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider>が使用され、を実装する場合 `T` `IDisposable` :</span><span class="sxs-lookup"><span data-stu-id="8a07f-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="8a07f-135">プールに返され ***ない*** 項目は破棄されます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-135">Items that are ***not*** returned to the pool will be disposed.</span></span>
* <span data-ttu-id="8a07f-136">プールが DI によって破棄されると、プール内のすべての項目が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-136">When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="8a07f-137">注: プールが破棄された後:</span><span class="sxs-lookup"><span data-stu-id="8a07f-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="8a07f-138">を呼び出す `Get` と、がスローさ `ObjectDisposedException` れます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="8a07f-139">`return` 指定された項目を破棄します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="8a07f-140">ObjectPool サンプル</span><span class="sxs-lookup"><span data-stu-id="8a07f-140">ObjectPool sample</span></span>

<span data-ttu-id="8a07f-141">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-141">The following code:</span></span>

* <span data-ttu-id="8a07f-142">`ObjectPoolProvider`[依存関係挿入](xref:fundamentals/dependency-injection)(DI) コンテナーにを追加します。</span><span class="sxs-lookup"><span data-stu-id="8a07f-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="8a07f-143">DI コンテナーにを追加して構成し `ObjectPool<StringBuilder>` ます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="8a07f-144">を追加し `BirthdayMiddleware` ます。</span><span class="sxs-lookup"><span data-stu-id="8a07f-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="8a07f-145">次のコードはを実装します。 `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="8a07f-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
