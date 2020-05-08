---
title: ASP.NET Core のメモリ管理とパターン
author: rick-anderson
description: ASP.NET Core でのメモリの管理方法とガベージコレクター (GC) の動作について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/memory
ms.openlocfilehash: db6f8e867fc83a211170aa59f5bad604d9c2730d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776117"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="0cd32-103">ASP.NET Core のメモリ管理とガベージコレクション (GC)</span><span class="sxs-lookup"><span data-stu-id="0cd32-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="0cd32-104">[Sébastien Ros](https://github.com/sebastienros)と[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0cd32-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0cd32-105">メモリ管理は、.NET などのマネージフレームワークでも複雑です。</span><span class="sxs-lookup"><span data-stu-id="0cd32-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="0cd32-106">メモリの問題を分析して理解することは困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="0cd32-107">この記事の内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-107">This article:</span></span>

* <span data-ttu-id="0cd32-108">多くの*メモリリークが発生*し、GC が動作して*いない*問題が発生しました。</span><span class="sxs-lookup"><span data-stu-id="0cd32-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="0cd32-109">これらの問題のほとんどは、.NET Core でのメモリ消費のしくみを理解していないか、測定方法を理解していないことによって発生しました。</span><span class="sxs-lookup"><span data-stu-id="0cd32-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="0cd32-110">問題のあるメモリ使用方法を示し、別のアプローチを提案します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="0cd32-111">.NET Core でのガベージコレクション (GC) のしくみ</span><span class="sxs-lookup"><span data-stu-id="0cd32-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="0cd32-112">GC は、各セグメントが連続するメモリ範囲であるヒープセグメントを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="0cd32-113">ヒープに配置されたオブジェクトは、0、1、または2の3つのジェネレーションに分類されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="0cd32-114">生成によって、アプリケーションによって参照されなくなったマネージオブジェクトのメモリを GC が解放する頻度が決まります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="0cd32-115">下位の番号付きジェネレーションは、GC の方が頻繁に行われます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="0cd32-116">オブジェクトは、有効期間に基づいて、ある世代から別の世代に移動されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="0cd32-117">オブジェクトが長くなると、オブジェクトは上位世代に移動されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="0cd32-118">既に説明したように、より高い世代は GC の方が頻繁に発生します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="0cd32-119">短期有効期間オブジェクトは常にジェネレーション0に残ります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="0cd32-120">たとえば、web 要求の有効期間中に参照されるオブジェクトは、短時間で終了します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="0cd32-121">一般に、アプリケーションレベル[シングルトン](xref:fundamentals/dependency-injection#service-lifetimes)は第2世代に移行します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="0cd32-122">ASP.NET Core アプリが開始されると、GC は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="0cd32-123">初期ヒープセグメント用にメモリを予約します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="0cd32-124">ランタイムが読み込まれるときに、メモリの一部をコミットします。</span><span class="sxs-lookup"><span data-stu-id="0cd32-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="0cd32-125">前のメモリ割り当ては、パフォーマンス上の理由から実行されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="0cd32-126">パフォーマンス上の利点は、連続したメモリのヒープセグメントから取得されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="0cd32-127">GC を呼び出します。収集</span><span class="sxs-lookup"><span data-stu-id="0cd32-127">Call GC.Collect</span></span>

<span data-ttu-id="0cd32-128">[GC を呼び出しています。](xref:System.GC.Collect*)明示的に収集:</span><span class="sxs-lookup"><span data-stu-id="0cd32-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="0cd32-129">運用 ASP.NET Core アプリでは**実行しないでください。**</span><span class="sxs-lookup"><span data-stu-id="0cd32-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="0cd32-130">は、メモリリークを調査するときに便利です。</span><span class="sxs-lookup"><span data-stu-id="0cd32-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="0cd32-131">調査時に、GC によってすべての未解決のオブジェクトがメモリから削除されたことを確認し、メモリを測定します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="0cd32-132">アプリのメモリ使用量の分析</span><span class="sxs-lookup"><span data-stu-id="0cd32-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="0cd32-133">専用ツールは、メモリ使用量の分析に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="0cd32-134">カウント (オブジェクト参照を)</span><span class="sxs-lookup"><span data-stu-id="0cd32-134">Counting object references</span></span>
- <span data-ttu-id="0cd32-135">GC が CPU 使用率に与える影響を測定する</span><span class="sxs-lookup"><span data-stu-id="0cd32-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="0cd32-136">各世代に使用されるメモリ領域の測定</span><span class="sxs-lookup"><span data-stu-id="0cd32-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="0cd32-137">メモリ使用量を分析するには、次のツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="0cd32-138">[dotnet](/dotnet/core/diagnostics/dotnet-trace): 運用コンピューターで使用できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="0cd32-139">Visual Studio デバッガーを使用せずにメモリ使用量を分析する</span><span class="sxs-lookup"><span data-stu-id="0cd32-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="0cd32-140">Visual Studio でのメモリ使用のプロファイリング</span><span class="sxs-lookup"><span data-stu-id="0cd32-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="0cd32-141">メモリの問題の検出</span><span class="sxs-lookup"><span data-stu-id="0cd32-141">Detecting memory issues</span></span>

<span data-ttu-id="0cd32-142">タスクマネージャーを使用して、ASP.NET アプリが使用しているメモリの量を把握できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="0cd32-143">タスクマネージャーのメモリ値:</span><span class="sxs-lookup"><span data-stu-id="0cd32-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="0cd32-144">ASP.NET プロセスによって使用されるメモリの量を表します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="0cd32-145">アプリの生きたオブジェクトや、ネイティブメモリ使用量などの他のメモリコンシューマーを含みます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="0cd32-146">タスクマネージャーのメモリ値が無制限に増加し、フラット化されない場合、アプリにはメモリリークが発生します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="0cd32-147">次のセクションでは、いくつかのメモリ使用パターンについて説明し、説明します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="0cd32-148">ディスプレイメモリ使用量アプリのサンプル</span><span class="sxs-lookup"><span data-stu-id="0cd32-148">Sample display memory usage app</span></span>

<span data-ttu-id="0cd32-149">[Memoryleak サンプルアプリ](https://github.com/sebastienros/memoryleak)は GitHub で入手できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="0cd32-150">MemoryLeak アプリ:</span><span class="sxs-lookup"><span data-stu-id="0cd32-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="0cd32-151">には、アプリのリアルタイムメモリおよび GC データを収集する診断コントローラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="0cd32-152">には、メモリおよび GC データを表示するインデックスページがあります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="0cd32-153">インデックスページは、1秒ごとに更新されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="0cd32-154">には、さまざまなメモリ読み込みパターンを提供する API コントローラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="0cd32-155">はサポートされているツールではありませんが、ASP.NET Core アプリのメモリ使用量パターンを表示するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="0cd32-156">MemoryLeak を実行します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-156">Run MemoryLeak.</span></span> <span data-ttu-id="0cd32-157">割り当てられたメモリは、GC が発生するまで徐々に増加します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="0cd32-158">データをキャプチャするためのカスタムオブジェクトがツールによって割り当てられるため、メモリが増加します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="0cd32-159">次の図は、Gen 0 GC が発生したときの MemoryLeak インデックスページを示しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="0cd32-160">API コントローラーからの API エンドポイントが呼び出されていないため、グラフには0個の RPS (1 秒あたりの要求数) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![前のグラフ](memory/_static/0RPS.png)

<span data-ttu-id="0cd32-162">グラフには、メモリ使用量の2つの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="0cd32-163">割り当て済み: マネージオブジェクトによって占有されているメモリの量</span><span class="sxs-lookup"><span data-stu-id="0cd32-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="0cd32-164">[Working set](/windows/win32/memory/working-set): 現在物理メモリに常駐しているプロセスの仮想アドレス空間にあるページのセット。</span><span class="sxs-lookup"><span data-stu-id="0cd32-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="0cd32-165">表示される作業セットは、タスクマネージャーに表示される値と同じです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="0cd32-166">一時オブジェクト</span><span class="sxs-lookup"><span data-stu-id="0cd32-166">Transient objects</span></span>

<span data-ttu-id="0cd32-167">次の API は、10 KB の文字列インスタンスを作成し、それをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="0cd32-168">各要求では、新しいオブジェクトがメモリに割り当てられ、応答に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="0cd32-169">文字列は .NET で UTF-16 文字として格納されるため、各文字はメモリ内で2バイトを取ります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="0cd32-170">次のグラフは、の負荷が比較的小さい場合に生成され、メモリ割り当てが GC によってどのように影響されているかを示します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![前のグラフ](memory/_static/bigstring.png)

<span data-ttu-id="0cd32-172">前のグラフは次を示しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-172">The preceding chart shows:</span></span>

* <span data-ttu-id="0cd32-173">4K RPS (1 秒あたりの要求数)。</span><span class="sxs-lookup"><span data-stu-id="0cd32-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="0cd32-174">ジェネレーション0の GC コレクションは、約2秒ごとに発生します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="0cd32-175">ワーキングセットは約 500 MB に固定されています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="0cd32-176">CPU は12% です。</span><span class="sxs-lookup"><span data-stu-id="0cd32-176">CPU is 12%.</span></span>
* <span data-ttu-id="0cd32-177">メモリ使用量と解放 (GC 経由) が安定しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="0cd32-178">次のグラフは、マシンで処理できる最大スループットで取得されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![前のグラフ](memory/_static/bigstring2.png)

<span data-ttu-id="0cd32-180">前のグラフは次を示しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-180">The preceding chart shows:</span></span>

* <span data-ttu-id="0cd32-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="0cd32-181">22K RPS</span></span>
* <span data-ttu-id="0cd32-182">ジェネレーション0の GC コレクションは1秒間に数回発生します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="0cd32-183">ジェネレーション1のコレクションがトリガーされるのは、アプリによって1秒あたりにかなり多くのメモリが割り当てられたためです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="0cd32-184">ワーキングセットは約 500 MB に固定されています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="0cd32-185">CPU は33% です。</span><span class="sxs-lookup"><span data-stu-id="0cd32-185">CPU is 33%.</span></span>
* <span data-ttu-id="0cd32-186">メモリ使用量と解放 (GC 経由) が安定しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="0cd32-187">CPU (33%)は過剰に使用されていないため、ガベージコレクションは多くの割り当てを保持できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="0cd32-188">ワークステーション GC とサーバー GC</span><span class="sxs-lookup"><span data-stu-id="0cd32-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="0cd32-189">.NET ガベージコレクターには、次の2つの異なるモードがあります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="0cd32-190">**WORKSTATION GC**: デスクトップ用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="0cd32-191">**サーバー GC**。</span><span class="sxs-lookup"><span data-stu-id="0cd32-191">**Server GC**.</span></span> <span data-ttu-id="0cd32-192">ASP.NET Core アプリの既定の GC。</span><span class="sxs-lookup"><span data-stu-id="0cd32-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="0cd32-193">サーバーに合わせて最適化されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-193">Optimized for the server.</span></span>

<span data-ttu-id="0cd32-194">GC モードは、プロジェクトファイルまたは発行されたアプリの*runtimeconfig. json*ファイルで明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="0cd32-195">次のマークアップは`ServerGarbageCollection` 、プロジェクトファイルの設定を示しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="0cd32-196">プロジェクト`ServerGarbageCollection`ファイルでを変更するには、アプリを再構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="0cd32-197">**注:** サーバーのガベージコレクションは、コアが1つのマシンでは使用でき**ません**。</span><span class="sxs-lookup"><span data-stu-id="0cd32-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="0cd32-198">詳細については、「<xref:System.Runtime.GCSettings.IsServerGC>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0cd32-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="0cd32-199">次の図は、ワークステーション GC を使用した 5K RPS のメモリプロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![前のグラフ](memory/_static/workstation.png)

<span data-ttu-id="0cd32-201">このグラフとサーバーのバージョンの違いは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="0cd32-202">ワーキングセットは 500 MB から 70 MB になります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="0cd32-203">GC は、2秒ごとではなく、1秒あたり複数回ジェネレーション0のコレクションを行います。</span><span class="sxs-lookup"><span data-stu-id="0cd32-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="0cd32-204">GC は 300 MB から 10 MB に減少します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="0cd32-205">一般的な web サーバー環境では、CPU 使用率はメモリよりも重要であるため、サーバー GC の方が適しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="0cd32-206">メモリ使用率が高く、CPU 使用率が比較的低い場合、ワークステーションの GC のパフォーマンスが向上する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="0cd32-207">たとえば、メモリが不足している複数の web アプリをホストしている高密度です。</span><span class="sxs-lookup"><span data-stu-id="0cd32-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="0cd32-208">ドッカーと小さなコンテナを使用した GC</span><span class="sxs-lookup"><span data-stu-id="0cd32-208">GC using Docker and small containers</span></span>

<span data-ttu-id="0cd32-209">1台のコンピューターで複数のコンテナー化されたアプリが実行されている場合、ワークステーションの GC はサーバー GC よりも preformant になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="0cd32-210">詳細については、「[小さなコンテナーでのサーバー gc の実行](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/)」と「[小さなコンテナーシナリオでサーバー gc を使用して実行する (パート 1-Gc ヒープのハードリミット)](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0cd32-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="0cd32-211">永続的なオブジェクト参照</span><span class="sxs-lookup"><span data-stu-id="0cd32-211">Persistent object references</span></span>

<span data-ttu-id="0cd32-212">GC は参照されているオブジェクトを解放できません。</span><span class="sxs-lookup"><span data-stu-id="0cd32-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="0cd32-213">参照されていて、不要になったオブジェクトは、メモリリークを発生させます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="0cd32-214">アプリがオブジェクトを頻繁に割り当てて、不要になった後にオブジェクトを解放できない場合は、メモリ使用量が時間の経過と共に増加します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="0cd32-215">次の API は、10 KB の文字列インスタンスを作成し、それをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="0cd32-216">前の例との違いは、このインスタンスが静的メンバーによって参照されていることです。これは、コレクションでは使用できないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="0cd32-217">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-217">The preceding code:</span></span>

* <span data-ttu-id="0cd32-218">は、一般的なメモリリークの例です。</span><span class="sxs-lookup"><span data-stu-id="0cd32-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="0cd32-219">頻繁な呼び出しでは、 `OutOfMemory`例外によりプロセスがクラッシュするまで、アプリのメモリが増加します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![前のグラフ](memory/_static/eternal.png)

<span data-ttu-id="0cd32-221">前の図では、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-221">In the preceding image:</span></span>

* <span data-ttu-id="0cd32-222">エンドポイントを`/api/staticstring`ロードテストすると、メモリが直線的に増加します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="0cd32-223">GC は、ジェネレーション2のコレクションを呼び出すことによって、メモリの負荷が増加したときにメモリの解放を試みます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="0cd32-224">GC では、リークしたメモリを解放できません。</span><span class="sxs-lookup"><span data-stu-id="0cd32-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="0cd32-225">割り当てられたワーキングセットは時間と共に増加します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="0cd32-226">キャッシュなどの一部のシナリオでは、メモリ不足によってオブジェクト参照が強制的に解放されるまで、オブジェクト参照を保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="0cd32-227">クラス<xref:System.WeakReference>は、この種のキャッシュコードに使用できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="0cd32-228">`WeakReference`オブジェクトはメモリ負荷の下で収集されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="0cd32-229">の既定の<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>実装で`WeakReference`は、が使用されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="0cd32-230">ネイティブメモリ</span><span class="sxs-lookup"><span data-stu-id="0cd32-230">Native memory</span></span>

<span data-ttu-id="0cd32-231">.NET Core オブジェクトの中には、ネイティブメモリに依存しているものがあります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="0cd32-232">GC でネイティブメモリを収集することはでき**ません**。</span><span class="sxs-lookup"><span data-stu-id="0cd32-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="0cd32-233">ネイティブメモリを使用している .NET オブジェクトは、ネイティブコードを使用して解放する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="0cd32-234">.NET には<xref:System.IDisposable> 、開発者がネイティブメモリを解放できるようにするインターフェイスが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="0cd32-235">が呼び出さ<xref:System.IDisposable.Dispose*>れていない場合でも、 `Dispose` [ファイナライザー](/dotnet/csharp/programming-guide/classes-and-structs/destructors)の実行時に、正しく実装されたクラスが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="0cd32-236">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="0cd32-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="0cd32-237">[Physicalfileprovider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0)はマネージクラスであるため、すべてのインスタンスが要求の最後に収集されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="0cd32-238">次の図は、API を`fileprovider`継続的に呼び出すときのメモリプロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![前のグラフ](memory/_static/fileprovider.png)

<span data-ttu-id="0cd32-240">前のグラフは、このクラスの実装に関する明らかな問題を示しています。これは、メモリ使用量が増加し続けるためです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="0cd32-241">これは、[この問題](https://github.com/dotnet/aspnetcore/issues/3110)で追跡されている既知の問題です。</span><span class="sxs-lookup"><span data-stu-id="0cd32-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="0cd32-242">次のいずれかの方法で、ユーザーコードで同じリークが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="0cd32-243">クラスを正しく解放できません。</span><span class="sxs-lookup"><span data-stu-id="0cd32-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="0cd32-244">破棄する依存オブジェクト`Dispose`のメソッドを呼び出そうとしていません。</span><span class="sxs-lookup"><span data-stu-id="0cd32-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="0cd32-245">ラージオブジェクトヒープ</span><span class="sxs-lookup"><span data-stu-id="0cd32-245">Large objects heap</span></span>

<span data-ttu-id="0cd32-246">メモリの割り当てや空きサイクルが頻繁に発生する場合は、特にメモリの大量のチャンクを割り当てるときにメモリをフラグメント化できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="0cd32-247">オブジェクトは、連続したメモリブロックで割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="0cd32-248">断片化を軽減するために、GC によってメモリが解放されると、断片化が解消されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="0cd32-249">このプロセスは、**圧縮**と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-249">This process is called **compaction**.</span></span> <span data-ttu-id="0cd32-250">圧縮には、オブジェクトの移動が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-250">Compaction involves moving objects.</span></span> <span data-ttu-id="0cd32-251">大きなオブジェクトを移動すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="0cd32-252">このため、GC は大きなオブジェクト[ヒープ](/dotnet/standard/garbage-collection/large-object-heap)(LOH) と呼ばれる_大きな_オブジェクト用に特別なメモリゾーンを作成します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="0cd32-253">85000バイトを超えるオブジェクト (約 83 KB) は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="0cd32-254">LOH に配置されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-254">Placed on the LOH.</span></span>
* <span data-ttu-id="0cd32-255">圧縮されていません。</span><span class="sxs-lookup"><span data-stu-id="0cd32-255">Not compacted.</span></span>
* <span data-ttu-id="0cd32-256">ジェネレーション2の Gc 中に収集されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="0cd32-257">LOH がいっぱいになると、GC はジェネレーション2のコレクションをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="0cd32-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="0cd32-258">ジェネレーション2のコレクション:</span><span class="sxs-lookup"><span data-stu-id="0cd32-258">Generation 2 collections:</span></span>

* <span data-ttu-id="0cd32-259">は本質的に低速です。</span><span class="sxs-lookup"><span data-stu-id="0cd32-259">Are inherently slow.</span></span>
* <span data-ttu-id="0cd32-260">さらに、他のすべての世代でコレクションをトリガーするコストも発生します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="0cd32-261">次のコードは、LOH を直ちに最適化します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="0cd32-262">LOH <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>の圧縮の詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0cd32-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="0cd32-263">.NET Core 3.0 以降を使用するコンテナーでは、LOH が自動的に圧縮されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="0cd32-264">次の API は、この動作を示しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="0cd32-265">次のグラフは、 `/api/loh/84975`エンドポイントを呼び出したときの、最大負荷下でのメモリプロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![前のグラフ](memory/_static/loh1.png)

<span data-ttu-id="0cd32-267">次のグラフは、 `/api/loh/84976`エンドポイントの呼び出しのメモリプロファイルを示しています。 *1 バイトだけ*割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![前のグラフ](memory/_static/loh2.png)

<span data-ttu-id="0cd32-269">注: `byte[]`構造体にはオーバーヘッドバイトがあります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="0cd32-270">そのため、84976バイトは85000の制限をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="0cd32-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="0cd32-271">前の2つのグラフを比較します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="0cd32-272">ワーキングセットは、2つのシナリオ (約 450 MB) で似ています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="0cd32-273">[LOH 要求 (84975 バイト)] の下には、ほとんどがジェネレーション0のコレクションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="0cd32-274">LOH を超える要求では、定数ジェネレーション2のコレクションが生成されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="0cd32-275">ジェネレーション2のコレクションはコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="0cd32-276">より多くの CPU が必要であり、スループットは約50% 低下します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="0cd32-277">一時的なラージオブジェクトは、gen2 Gc を引き起こすため、特に問題になります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="0cd32-278">最大のパフォーマンスを向上させるには、大きなオブジェクトの使用を最小限にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="0cd32-279">可能であれば、大きなオブジェクトを分割します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-279">If possible, split up large objects.</span></span> <span data-ttu-id="0cd32-280">たとえば、ASP.NET Core の[応答キャッシュ](xref:performance/caching/response)ミドルウェアでは、キャッシュエントリが85000バイト未満のブロックに分割されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="0cd32-281">次のリンクでは、オブジェクトを LOH の制限下に維持するための ASP.NET Core アプローチについて説明します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="0cd32-282">ResponseCaching/Streams/StreamUtilities .cs</span><span class="sxs-lookup"><span data-stu-id="0cd32-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="0cd32-283">ResponseCaching/MemoryResponseCache</span><span class="sxs-lookup"><span data-stu-id="0cd32-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="0cd32-284">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0cd32-284">For more information, see:</span></span>

* [<span data-ttu-id="0cd32-285">大きなオブジェクトヒープが漏れています</span><span class="sxs-lookup"><span data-stu-id="0cd32-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="0cd32-286">大きなオブジェクトヒープ</span><span class="sxs-lookup"><span data-stu-id="0cd32-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="0cd32-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="0cd32-287">HttpClient</span></span>

<span data-ttu-id="0cd32-288">を誤っ<xref:System.Net.Http.HttpClient>て使用すると、リソースリークが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="0cd32-289">データベース接続、ソケット、ファイルハンドルなどのシステムリソース:</span><span class="sxs-lookup"><span data-stu-id="0cd32-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="0cd32-290">はメモリよりも不足しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="0cd32-291">メモリよりもリークが発生した場合、より問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="0cd32-292">経験豊富な .NET 開発者<xref:System.IDisposable.Dispose*>は、を実装<xref:System.IDisposable>するオブジェクトに対してを呼び出すことがわかっています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="0cd32-293">を実装`IDisposable`するオブジェクトを破棄しないと、メモリリークやシステムリソースのリークが発生します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="0cd32-294">`HttpClient`は`IDisposable`を実装しますが、すべての呼び出しで破棄することはでき**ません**。</span><span class="sxs-lookup"><span data-stu-id="0cd32-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="0cd32-295">代わりに、 `HttpClient`を再利用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="0cd32-296">次のエンドポイントは、すべての`HttpClient`要求に対して新しいインスタンスを作成し、破棄します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="0cd32-297">負荷の下で、次のエラーメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="0cd32-298">`HttpClient`インスタンスが破棄されている場合でも、オペレーティングシステムによって実際のネットワーク接続が解放されるまでに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="0cd32-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="0cd32-299">新しい接続を継続的に作成することで、_ポートの枯渇_が発生します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="0cd32-300">各クライアント接続には、独自のクライアントポートが必要です。</span><span class="sxs-lookup"><span data-stu-id="0cd32-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="0cd32-301">ポートの枯渇を防ぐ方法の1つは、 `HttpClient`同じインスタンスを再利用することです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="0cd32-302">`HttpClient`インスタンスは、アプリが停止したときに解放されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="0cd32-303">この例では、すべての破棄可能なリソースを使用後に破棄する必要がないことを示しています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="0cd32-304">`HttpClient`インスタンスの有効期間をより適切に処理する方法については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0cd32-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="0cd32-305">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="0cd32-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="0cd32-306">HTTPClient ファクトリのブログ</span><span class="sxs-lookup"><span data-stu-id="0cd32-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="0cd32-307">オブジェクトプール</span><span class="sxs-lookup"><span data-stu-id="0cd32-307">Object pooling</span></span>

<span data-ttu-id="0cd32-308">前の例では、 `HttpClient`インスタンスを静的にし、すべての要求で再利用する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="0cd32-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="0cd32-309">再利用すると、リソースが不足するのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="0cd32-310">オブジェクトプール:</span><span class="sxs-lookup"><span data-stu-id="0cd32-310">Object pooling:</span></span>

* <span data-ttu-id="0cd32-311">再利用パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="0cd32-312">は、作成に負荷がかかるオブジェクト向けに設計されています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="0cd32-313">プールは、スレッド間で予約および解放できる事前に初期化されたオブジェクトのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="0cd32-314">プールでは、制限、事前定義されたサイズ、増加率などの割り当てルールを定義できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="0cd32-315">NuGet パッケージの[Microsoft extension. ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/)には、このようなプールの管理に役立つクラスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0cd32-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="0cd32-316">次の API エンドポイントは`byte` 、各要求に対してランダムな数値を格納するバッファーをインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="0cd32-317">次のグラフは、中程度の負荷で前の API を呼び出したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-317">The following chart display calling the preceding API with moderate load:</span></span>

![前のグラフ](memory/_static/array.png)

<span data-ttu-id="0cd32-319">前のグラフでは、ジェネレーション0のコレクションは1秒間に約1回発生します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="0cd32-320">上記のコードは、 `byte` [arraypool\<T>](xref:System.Buffers.ArrayPool`1)を使用してバッファーをプールすることによって最適化できます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="0cd32-321">静的インスタンスは、要求間で再利用されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="0cd32-322">この方法の違いは、プールされたオブジェクトが API から返されることです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="0cd32-323">ということは：</span><span class="sxs-lookup"><span data-stu-id="0cd32-323">That means:</span></span>

* <span data-ttu-id="0cd32-324">オブジェクトは、メソッドから戻るとすぐにコントロールから除外されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="0cd32-325">オブジェクトを解放することはできません。</span><span class="sxs-lookup"><span data-stu-id="0cd32-325">You can't release the object.</span></span>

<span data-ttu-id="0cd32-326">オブジェクトの破棄を設定するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="0cd32-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="0cd32-327">プールされた配列を破棄可能なオブジェクトにカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="0cd32-328">プールされたオブジェクトを[httpcontext.current](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)に登録します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="0cd32-329">`RegisterForDispose`は、HTTP 要求が`Dispose`完了したときにのみ解放されるように、ターゲットオブジェクトでを呼び出すことを処理します。</span><span class="sxs-lookup"><span data-stu-id="0cd32-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="0cd32-330">プールされていないバージョンと同じ負荷を適用すると、次のグラフが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0cd32-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![前のグラフ](memory/_static/pooledarray.png)

<span data-ttu-id="0cd32-332">主な違いは、バイトが割り当てられ、その結果、ジェネレーション0のコレクションがはるかに少ないことです。</span><span class="sxs-lookup"><span data-stu-id="0cd32-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cd32-333">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0cd32-333">Additional resources</span></span>

* [<span data-ttu-id="0cd32-334">ガベージ コレクション</span><span class="sxs-lookup"><span data-stu-id="0cd32-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="0cd32-335">同時実行ビジュアライザーを使用したさまざまな GC モードについて</span><span class="sxs-lookup"><span data-stu-id="0cd32-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="0cd32-336">大きなオブジェクトヒープが漏れています</span><span class="sxs-lookup"><span data-stu-id="0cd32-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="0cd32-337">大きなオブジェクトヒープ</span><span class="sxs-lookup"><span data-stu-id="0cd32-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
