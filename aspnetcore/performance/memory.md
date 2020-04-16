---
title: ASP.NETコアにおけるメモリ管理とパターン
author: rick-anderson
description: ASP.NET Core でメモリを管理する方法とガベージ コレクター (GC) のしくみについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440949"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="45258-103">ASP.NETコアのメモリ管理とガベージ コレクション (GC)</span><span class="sxs-lookup"><span data-stu-id="45258-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="45258-104">[セバスチャン・ロス](https://github.com/sebastienros)と[リック・アンダーソン](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="45258-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="45258-105">メモリ管理は、.NET などのマネージ フレームワークでも複雑です。</span><span class="sxs-lookup"><span data-stu-id="45258-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="45258-106">メモリの問題の分析と理解は困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="45258-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="45258-107">この記事の内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="45258-107">This article:</span></span>

* <span data-ttu-id="45258-108">多くのメモリ*リーク*と*GC が動作しない*問題によって動機づけられました。</span><span class="sxs-lookup"><span data-stu-id="45258-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="45258-109">これらの問題のほとんどは、.NET Core でのメモリ消費量のしくみを理解していないか、メモリ使用量の測定方法を理解していなかった場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="45258-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="45258-110">問題のあるメモリの使用を示し、代替アプローチを提案します。</span><span class="sxs-lookup"><span data-stu-id="45258-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="45258-111">NET Core でのガベージ コレクション (GC) の動作</span><span class="sxs-lookup"><span data-stu-id="45258-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="45258-112">GC は、各セグメントが連続したメモリ範囲であるヒープ セグメントを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="45258-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="45258-113">ヒープに配置されたオブジェクトは、0、1、または 2 の 3 つの世代のいずれかに分類されます。</span><span class="sxs-lookup"><span data-stu-id="45258-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="45258-114">生成によって、GC がアプリによって参照されなくなったマネージ オブジェクトのメモリ解放を試みる頻度が決まります。</span><span class="sxs-lookup"><span data-stu-id="45258-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="45258-115">番号が低い世代は GC の方が頻度が高くなります。</span><span class="sxs-lookup"><span data-stu-id="45258-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="45258-116">オブジェクトは、有効期間に基づいてある世代から別の世代に移動されます。</span><span class="sxs-lookup"><span data-stu-id="45258-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="45258-117">オブジェクトが長生きするにつれて、オブジェクトはより高い世代に移動されます。</span><span class="sxs-lookup"><span data-stu-id="45258-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="45258-118">前述のように、上位世代はGCの頻度が低くなります。</span><span class="sxs-lookup"><span data-stu-id="45258-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="45258-119">短期存続オブジェクトはジェネレーション 0 に常に残ります。</span><span class="sxs-lookup"><span data-stu-id="45258-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="45258-120">たとえば、Web 要求の存続期間中に参照されるオブジェクトは、有効期間が短い場合です。</span><span class="sxs-lookup"><span data-stu-id="45258-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="45258-121">アプリケーション レベル[のシングルトン](xref:fundamentals/dependency-injection#service-lifetimes)は、一般にジェネレーション 2 に移行します。</span><span class="sxs-lookup"><span data-stu-id="45258-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="45258-122">ASP.NET Core アプリが起動すると、GC は次の操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="45258-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="45258-123">初期ヒープ セグメント用にメモリを予約します。</span><span class="sxs-lookup"><span data-stu-id="45258-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="45258-124">ランタイムが読み込まれるときに、メモリの一部をコミットします。</span><span class="sxs-lookup"><span data-stu-id="45258-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="45258-125">上記のメモリ割り当ては、パフォーマンス上の理由から行われます。</span><span class="sxs-lookup"><span data-stu-id="45258-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="45258-126">パフォーマンスの利点は、連続したメモリ内のヒープ セグメントから得られます。</span><span class="sxs-lookup"><span data-stu-id="45258-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="45258-127">GC を呼び出します。収集</span><span class="sxs-lookup"><span data-stu-id="45258-127">Call GC.Collect</span></span>

<span data-ttu-id="45258-128">GC を呼び出しています[。](xref:System.GC.Collect*)明示的に収集する:</span><span class="sxs-lookup"><span data-stu-id="45258-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="45258-129">コア アプリの運用ASP.NET実行**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="45258-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="45258-130">メモリ リークを調査するときに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="45258-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="45258-131">調査を行う際に、GC がすべてのぶら下がっているオブジェクトをメモリから削除してメモリを測定できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="45258-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="45258-132">アプリのメモリ使用量の分析</span><span class="sxs-lookup"><span data-stu-id="45258-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="45258-133">専用ツールは、メモリ使用量の分析に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="45258-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="45258-134">オブジェクト参照のカウント</span><span class="sxs-lookup"><span data-stu-id="45258-134">Counting object references</span></span>
- <span data-ttu-id="45258-135">GC が CPU 使用率に与える影響の測定</span><span class="sxs-lookup"><span data-stu-id="45258-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="45258-136">各世代に使用されるメモリ空間の測定</span><span class="sxs-lookup"><span data-stu-id="45258-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="45258-137">メモリ使用量を分析するには、次のツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="45258-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="45258-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): 生産機で使用できます。</span><span class="sxs-lookup"><span data-stu-id="45258-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="45258-139">Visual Studio デバッガーを使用せずにメモリ使用量を分析する</span><span class="sxs-lookup"><span data-stu-id="45258-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="45258-140">Visual Studio でのメモリ使用のプロファイリング</span><span class="sxs-lookup"><span data-stu-id="45258-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="45258-141">メモリの問題の検出</span><span class="sxs-lookup"><span data-stu-id="45258-141">Detecting memory issues</span></span>

<span data-ttu-id="45258-142">タスク マネージャーを使用すると、アプリが使用しているメモリの量ASP.NET把握できます。</span><span class="sxs-lookup"><span data-stu-id="45258-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="45258-143">タスク マネージャのメモリ値:</span><span class="sxs-lookup"><span data-stu-id="45258-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="45258-144">ASP.NET プロセスによって使用されるメモリの量を表します。</span><span class="sxs-lookup"><span data-stu-id="45258-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="45258-145">アプリの生きたオブジェクトや、ネイティブメモリ使用量などの他のメモリコンシューマが含まれます。</span><span class="sxs-lookup"><span data-stu-id="45258-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="45258-146">タスク マネージャーのメモリ値が無限に増加し、フラット化しない場合、アプリはメモリ リークがあります。</span><span class="sxs-lookup"><span data-stu-id="45258-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="45258-147">次のセクションでは、いくつかのメモリ使用量パターンを示し、説明します。</span><span class="sxs-lookup"><span data-stu-id="45258-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="45258-148">ディスプレイメモリ使用量アプリのサンプル</span><span class="sxs-lookup"><span data-stu-id="45258-148">Sample display memory usage app</span></span>

<span data-ttu-id="45258-149">[メモリ リーク サンプル アプリ](https://github.com/sebastienros/memoryleak)は GitHub で入手できます。</span><span class="sxs-lookup"><span data-stu-id="45258-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="45258-150">メモリリークアプリ:</span><span class="sxs-lookup"><span data-stu-id="45258-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="45258-151">アプリのリアルタイム メモリと GC データを収集する診断コントローラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="45258-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="45258-152">メモリと GC データを表示するインデックス ページがあります。</span><span class="sxs-lookup"><span data-stu-id="45258-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="45258-153">インデックス ページは毎秒更新されます。</span><span class="sxs-lookup"><span data-stu-id="45258-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="45258-154">さまざまなメモリ ロード パターンを提供する API コントローラが含まれています。</span><span class="sxs-lookup"><span data-stu-id="45258-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="45258-155">サポートされていないツールですが、ASP.NET Core アプリのメモリ使用量パターンを表示するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="45258-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="45258-156">メモリ リークを実行します。</span><span class="sxs-lookup"><span data-stu-id="45258-156">Run MemoryLeak.</span></span> <span data-ttu-id="45258-157">割り当てられたメモリは、GC が発生するまで徐々に増加します。</span><span class="sxs-lookup"><span data-stu-id="45258-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="45258-158">ツールはデータをキャプチャするカスタム オブジェクトを割り当てるため、メモリが増加します。</span><span class="sxs-lookup"><span data-stu-id="45258-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="45258-159">次の図は、Gen 0 GC が発生した場合のメモリ リーク インデックス ページを示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="45258-160">API コントローラーから API エンドポイントが呼び出されていないので、グラフは 0 RPS (1 秒あたりの要求数) を示します。</span><span class="sxs-lookup"><span data-stu-id="45258-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![先行するグラフ](memory/_static/0RPS.png)

<span data-ttu-id="45258-162">このグラフには、メモリ使用量に関する 2 つの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="45258-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="45258-163">割り当て済み: 管理対象オブジェクトが占有するメモリの量</span><span class="sxs-lookup"><span data-stu-id="45258-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="45258-164">[ワーキング セット](/windows/win32/memory/working-set): 物理メモリに現在常駐しているプロセスの仮想アドレス空間内のページのセット。</span><span class="sxs-lookup"><span data-stu-id="45258-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="45258-165">表示されるワーキング セットは、タスク マネージャが表示する値と同じです。</span><span class="sxs-lookup"><span data-stu-id="45258-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="45258-166">一時的なオブジェクト</span><span class="sxs-lookup"><span data-stu-id="45258-166">Transient objects</span></span>

<span data-ttu-id="45258-167">次の API は、10 KB の String インスタンスを作成し、それをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="45258-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="45258-168">要求ごとに、新しいオブジェクトがメモリに割り当てられ、応答に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="45258-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="45258-169">文字列は UTF-16 文字として .NET に格納されるため、各文字はメモリ内で 2 バイトを使用します。</span><span class="sxs-lookup"><span data-stu-id="45258-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="45258-170">次のグラフは、GC によるメモリ割り当てへの影響を示すために、比較的小さな負荷で生成されます。</span><span class="sxs-lookup"><span data-stu-id="45258-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![先行するグラフ](memory/_static/bigstring.png)

<span data-ttu-id="45258-172">上記のグラフは、次の項目を示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-172">The preceding chart shows:</span></span>

* <span data-ttu-id="45258-173">4K RPS(1 秒あたりの要求数)。</span><span class="sxs-lookup"><span data-stu-id="45258-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="45258-174">ジェネレーション 0 の GC コレクションは、約 2 秒ごとに発生します。</span><span class="sxs-lookup"><span data-stu-id="45258-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="45258-175">ワーキング セットは約 500 MB で一定です。</span><span class="sxs-lookup"><span data-stu-id="45258-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="45258-176">CPU は 12% です。</span><span class="sxs-lookup"><span data-stu-id="45258-176">CPU is 12%.</span></span>
* <span data-ttu-id="45258-177">メモリの消費と放出(GCを介して)は安定しています。</span><span class="sxs-lookup"><span data-stu-id="45258-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="45258-178">次の図は、マシンで処理できる最大スループットで取得されます。</span><span class="sxs-lookup"><span data-stu-id="45258-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![先行するグラフ](memory/_static/bigstring2.png)

<span data-ttu-id="45258-180">上記のグラフは、次の項目を示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-180">The preceding chart shows:</span></span>

* <span data-ttu-id="45258-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="45258-181">22K RPS</span></span>
* <span data-ttu-id="45258-182">ジェネレーション 0 の GC コレクションは 1 秒あたりに複数回発生します。</span><span class="sxs-lookup"><span data-stu-id="45258-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="45258-183">アプリが 1 秒あたりに大幅に多くのメモリを割り当てたため、ジェネレーション 1 のコレクションがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="45258-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="45258-184">ワーキング セットは約 500 MB で一定です。</span><span class="sxs-lookup"><span data-stu-id="45258-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="45258-185">CPU は 33% です。</span><span class="sxs-lookup"><span data-stu-id="45258-185">CPU is 33%.</span></span>
* <span data-ttu-id="45258-186">メモリの消費と放出(GCを介して)は安定しています。</span><span class="sxs-lookup"><span data-stu-id="45258-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="45258-187">CPU (33%)過剰に使用されないため、ガベージ コレクションは多数の割り当てに追いつくことができます。</span><span class="sxs-lookup"><span data-stu-id="45258-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="45258-188">ワークステーション GC 対 サーバー GC</span><span class="sxs-lookup"><span data-stu-id="45258-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="45258-189">.NET ガベージ コレクタには、2 つの異なるモードがあります。</span><span class="sxs-lookup"><span data-stu-id="45258-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="45258-190">**ワークステーションGC:** デスクトップ用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="45258-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="45258-191">**サーバー GC**.</span><span class="sxs-lookup"><span data-stu-id="45258-191">**Server GC**.</span></span> <span data-ttu-id="45258-192">ASP.NETコア アプリの既定の GC。</span><span class="sxs-lookup"><span data-stu-id="45258-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="45258-193">サーバー用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="45258-193">Optimized for the server.</span></span>

<span data-ttu-id="45258-194">GC モードは、プロジェクト ファイルまたは公開されたアプリの*runtimeconfig.json*ファイルで明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="45258-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="45258-195">次のマークアップは、`ServerGarbageCollection`プロジェクト ファイルの設定を示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="45258-196">プロジェクト`ServerGarbageCollection`ファイルを変更するには、アプリを再構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="45258-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="45258-197">**注:** サーバーのガベージ コレクションは、単一コアのマシンでは使用**できません**。</span><span class="sxs-lookup"><span data-stu-id="45258-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="45258-198">詳細については、「<xref:System.Runtime.GCSettings.IsServerGC>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="45258-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="45258-199">次の図は、ワークステーション GC を使用する 5K RPS のメモリ プロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![先行するグラフ](memory/_static/workstation.png)

<span data-ttu-id="45258-201">このグラフとサーバーバージョンの違いは、次の点で大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="45258-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="45258-202">ワーキング セットは 500 MB から 70 MB に低下します。</span><span class="sxs-lookup"><span data-stu-id="45258-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="45258-203">GC は、ジェネレーション 0 のコレクションを 2 秒ごとにではなく、1 秒に複数回収集します。</span><span class="sxs-lookup"><span data-stu-id="45258-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="45258-204">GC は 300 MB から 10 MB にドロップします。</span><span class="sxs-lookup"><span data-stu-id="45258-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="45258-205">一般的な Web サーバー環境では、CPU 使用率はメモリよりも重要であるため、サーバー GC の方が優れています。</span><span class="sxs-lookup"><span data-stu-id="45258-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="45258-206">メモリ使用率が高く、CPU 使用率が比較的低い場合、ワークステーション GC の方がパフォーマンスが高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="45258-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="45258-207">たとえば、メモリが不足している複数の Web アプリをホストする高密度です。</span><span class="sxs-lookup"><span data-stu-id="45258-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="45258-208">ドッカーと小さなコンテナを使用したGC</span><span class="sxs-lookup"><span data-stu-id="45258-208">GC using Docker and small containers</span></span>

<span data-ttu-id="45258-209">1 台のコンピューターで複数のコンテナー化されたアプリケーションが実行されている場合、ワークステーション GC はサーバー GC よりもプリフォームが多い可能性があります。</span><span class="sxs-lookup"><span data-stu-id="45258-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="45258-210">詳細については、「[サーバー GC を小さなコンテナーで実行](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/)する」および[「小さなコンテナー シナリオでの Server GC での実行 パート 1 – GC ヒープのハードリミット](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="45258-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="45258-211">永続オブジェクト参照</span><span class="sxs-lookup"><span data-stu-id="45258-211">Persistent object references</span></span>

<span data-ttu-id="45258-212">GC は、参照されているオブジェクトを解放できません。</span><span class="sxs-lookup"><span data-stu-id="45258-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="45258-213">参照されているが不要になったオブジェクトは、メモリ リークを発生させます。</span><span class="sxs-lookup"><span data-stu-id="45258-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="45258-214">アプリが頻繁にオブジェクトを割り当て、不要になった後にオブジェクトを解放できない場合、時間の経過とともにメモリ使用量が増加します。</span><span class="sxs-lookup"><span data-stu-id="45258-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="45258-215">次の API は、10 KB の String インスタンスを作成し、それをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="45258-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="45258-216">前の例との違いは、このインスタンスが静的メンバーによって参照されることです。</span><span class="sxs-lookup"><span data-stu-id="45258-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

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

<span data-ttu-id="45258-217">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="45258-217">The preceding code:</span></span>

* <span data-ttu-id="45258-218">典型的なメモリ リークの例です。</span><span class="sxs-lookup"><span data-stu-id="45258-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="45258-219">頻繁な呼び出しでは、例外を伴ってプロセスがクラッシュするまで`OutOfMemory`、アプリのメモリが増加します。</span><span class="sxs-lookup"><span data-stu-id="45258-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![先行するグラフ](memory/_static/eternal.png)

<span data-ttu-id="45258-221">上記のイメージでは、</span><span class="sxs-lookup"><span data-stu-id="45258-221">In the preceding image:</span></span>

* <span data-ttu-id="45258-222">エンドポイントの負荷`/api/staticstring`テストにより、メモリが線形に増加します。</span><span class="sxs-lookup"><span data-stu-id="45258-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="45258-223">GC は、ジェネレーション 2 のコレクションを呼び出すことによって、メモリの負荷が増大するに応じてメモリを解放しようとします。</span><span class="sxs-lookup"><span data-stu-id="45258-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="45258-224">GC は、リークされたメモリを解放できません。</span><span class="sxs-lookup"><span data-stu-id="45258-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="45258-225">時間と共に割り当て済みおよびワーキング セットが増加します。</span><span class="sxs-lookup"><span data-stu-id="45258-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="45258-226">キャッシュなどの一部のシナリオでは、メモリの圧迫によって解放されるまで、オブジェクト参照を保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="45258-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="45258-227">この<xref:System.WeakReference>クラスは、この種類のキャッシュ コードに使用できます。</span><span class="sxs-lookup"><span data-stu-id="45258-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="45258-228">`WeakReference`メモリの負荷の下でオブジェクトが収集されます。</span><span class="sxs-lookup"><span data-stu-id="45258-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="45258-229">の既定の<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>実装では`WeakReference`、 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="45258-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="45258-230">ネイティブメモリ</span><span class="sxs-lookup"><span data-stu-id="45258-230">Native memory</span></span>

<span data-ttu-id="45258-231">一部の .NET Core オブジェクトは、ネイティブ メモリに依存しています。</span><span class="sxs-lookup"><span data-stu-id="45258-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="45258-232">ネイティブメモリは、GC では収集**できません**。</span><span class="sxs-lookup"><span data-stu-id="45258-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="45258-233">ネイティブ メモリを使用する .NET オブジェクトは、ネイティブ コードを使用して解放する必要があります。</span><span class="sxs-lookup"><span data-stu-id="45258-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="45258-234">.NET は<xref:System.IDisposable>、ネイティブ メモリを解放するためのインターフェイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="45258-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="45258-235">呼<xref:System.IDisposable.Dispose*>び出されていなくても、[ファイナライザー](/dotnet/csharp/programming-guide/classes-and-structs/destructors)の実行時`Dispose`に正しく実装されたクラスが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="45258-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="45258-236">次のコードについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="45258-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="45258-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0)はマネージ クラスであるため、すべてのインスタンスは要求の最後に収集されます。</span><span class="sxs-lookup"><span data-stu-id="45258-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="45258-238">次の図は、API を継続的に呼`fileprovider`び出している間のメモリ プロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![先行するグラフ](memory/_static/fileprovider.png)

<span data-ttu-id="45258-240">上記の表は、メモリ使用量が増加し続けているため、このクラスの実装に関する明白な問題を示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="45258-241">これは、この問題で追跡されている既知の[問題](https://github.com/dotnet/aspnetcore/issues/3110)です。</span><span class="sxs-lookup"><span data-stu-id="45258-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="45258-242">ユーザー コードで、次のいずれかの方法で同じリークが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="45258-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="45258-243">クラスが正しく解放されない。</span><span class="sxs-lookup"><span data-stu-id="45258-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="45258-244">破棄する必要がある依存`Dispose`オブジェクトのメソッドを呼び出す忘れ。</span><span class="sxs-lookup"><span data-stu-id="45258-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="45258-245">ラージ オブジェクト ヒープ</span><span class="sxs-lookup"><span data-stu-id="45258-245">Large objects heap</span></span>

<span data-ttu-id="45258-246">メモリの割り当て/解放サイクルが頻繁に発生すると、特にメモリの大きなチャンクを割り当てる場合に、メモリが断片化される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="45258-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="45258-247">オブジェクトは、連続したメモリ ブロックに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="45258-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="45258-248">断片化を軽減するために、GC がメモリを解放すると、最適化を試みます。</span><span class="sxs-lookup"><span data-stu-id="45258-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="45258-249">このプロセスは**圧縮**と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="45258-249">This process is called **compaction**.</span></span> <span data-ttu-id="45258-250">圧縮にはオブジェクトの移動が含まれます。</span><span class="sxs-lookup"><span data-stu-id="45258-250">Compaction involves moving objects.</span></span> <span data-ttu-id="45258-251">大きなオブジェクトを移動すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="45258-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="45258-252">このため、GC はラージ オブジェクト ヒープ (LOH) と呼ばれる _、ラージ_[オブジェクト](/dotnet/standard/garbage-collection/large-object-heap)用の特別なメモリ ゾーンを作成します。</span><span class="sxs-lookup"><span data-stu-id="45258-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="45258-253">85,000 バイト (約 83 KB) を超えるオブジェクトは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="45258-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="45258-254">LOHに配置されます。</span><span class="sxs-lookup"><span data-stu-id="45258-254">Placed on the LOH.</span></span>
* <span data-ttu-id="45258-255">圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="45258-255">Not compacted.</span></span>
* <span data-ttu-id="45258-256">第 2 世代 GC の間に収集されます。</span><span class="sxs-lookup"><span data-stu-id="45258-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="45258-257">LOH が満杯になると、GC はジェネレーション 2 のコレクションをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="45258-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="45258-258">第 2 世代コレクション:</span><span class="sxs-lookup"><span data-stu-id="45258-258">Generation 2 collections:</span></span>

* <span data-ttu-id="45258-259">本質的に遅いです。</span><span class="sxs-lookup"><span data-stu-id="45258-259">Are inherently slow.</span></span>
* <span data-ttu-id="45258-260">さらに、他のすべての世代でコレクションをトリガーするコストが発生します。</span><span class="sxs-lookup"><span data-stu-id="45258-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="45258-261">次のコードは、LOH を直ちに圧縮します。</span><span class="sxs-lookup"><span data-stu-id="45258-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="45258-262">LOH の圧縮については、「」を参照してください<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>。</span><span class="sxs-lookup"><span data-stu-id="45258-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="45258-263">NET Core 3.0 以降を使用するコンテナーでは、LOH は自動的に圧縮されます。</span><span class="sxs-lookup"><span data-stu-id="45258-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="45258-264">この動作を示す次の API を示します。</span><span class="sxs-lookup"><span data-stu-id="45258-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="45258-265">次のグラフは、最大負荷の下で`/api/loh/84975`エンドポイントを呼び出すメモリ プロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![先行するグラフ](memory/_static/loh1.png)

<span data-ttu-id="45258-267">次の図は、`/api/loh/84976`エンドポイントを呼び出し、もう*1 バイトを*割り当てるメモリ プロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![先行するグラフ](memory/_static/loh2.png)

<span data-ttu-id="45258-269">注:`byte[]`この構造体にはオーバーヘッド・バイトがあります。</span><span class="sxs-lookup"><span data-stu-id="45258-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="45258-270">84,976 バイトが 85,000 の制限をトリガーする理由です。</span><span class="sxs-lookup"><span data-stu-id="45258-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="45258-271">上記の 2 つのグラフを比較します。</span><span class="sxs-lookup"><span data-stu-id="45258-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="45258-272">ワーキング セットは、両方のシナリオで約 450 MB に似ています。</span><span class="sxs-lookup"><span data-stu-id="45258-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="45258-273">LOH 要求 (84,975 バイト) の下に、大部分の世代 0 のコレクションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="45258-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="45258-274">オーバー LOH 要求は、定数ジェネレーション 2 のコレクションを生成します。</span><span class="sxs-lookup"><span data-stu-id="45258-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="45258-275">ジェネレーション 2 のコレクションは高価です。</span><span class="sxs-lookup"><span data-stu-id="45258-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="45258-276">CPU が必要になり、スループットがほぼ 50% 低下します。</span><span class="sxs-lookup"><span data-stu-id="45258-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="45258-277">一時的な大きなオブジェクトは gen2 GC を引き起こすので特に問題があります。</span><span class="sxs-lookup"><span data-stu-id="45258-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="45258-278">パフォーマンスを最大限に高めるには、大きなオブジェクトの使用を最小限に抑える必要があります。</span><span class="sxs-lookup"><span data-stu-id="45258-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="45258-279">可能であれば、大きなオブジェクトを分割します。</span><span class="sxs-lookup"><span data-stu-id="45258-279">If possible, split up large objects.</span></span> <span data-ttu-id="45258-280">たとえば、ASP.NET Core の[応答キャッシュ](xref:performance/caching/response)ミドルウェアは、キャッシュ エントリを 85,000 バイト未満のブロックに分割します。</span><span class="sxs-lookup"><span data-stu-id="45258-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="45258-281">以下のリンクは、オブジェクトを LOH 制限の下に維持するためのASP.NETコアアプローチを示しています。</span><span class="sxs-lookup"><span data-stu-id="45258-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="45258-282">応答キャッシュ/ストリーム/ストリームユーティリティ.cs</span><span class="sxs-lookup"><span data-stu-id="45258-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="45258-283">応答キャッシュ/メモリレスポンスキャッシュ.cs</span><span class="sxs-lookup"><span data-stu-id="45258-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="45258-284">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="45258-284">For more information, see:</span></span>

* [<span data-ttu-id="45258-285">ラージ オブジェクト ヒープが見つけられます</span><span class="sxs-lookup"><span data-stu-id="45258-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="45258-286">ラージ オブジェクト ヒープ</span><span class="sxs-lookup"><span data-stu-id="45258-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="45258-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="45258-287">HttpClient</span></span>

<span data-ttu-id="45258-288">誤って使用<xref:System.Net.Http.HttpClient>すると、リソース リークが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="45258-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="45258-289">データベース接続、ソケット、ファイル ハンドルなどのシステム リソース:</span><span class="sxs-lookup"><span data-stu-id="45258-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="45258-290">記憶よりも不足しています。</span><span class="sxs-lookup"><span data-stu-id="45258-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="45258-291">メモリよりもリークした場合、より問題があります。</span><span class="sxs-lookup"><span data-stu-id="45258-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="45258-292">経験豊富な .NET 開発者<xref:System.IDisposable.Dispose*>は、 を<xref:System.IDisposable>実装するオブジェクトを呼び出すことを知っています。</span><span class="sxs-lookup"><span data-stu-id="45258-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="45258-293">実装`IDisposable`するオブジェクトを破棄しないと、通常はメモリリークやシステム リソースのリークが発生します。</span><span class="sxs-lookup"><span data-stu-id="45258-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="45258-294">`HttpClient`は`IDisposable`、 を実装しますが、呼び出しのたびに破棄**されるわけではありません**。</span><span class="sxs-lookup"><span data-stu-id="45258-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="45258-295">むしろ、`HttpClient`再利用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="45258-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="45258-296">次のエンドポイントは、要求ごとに新`HttpClient`しいインスタンスを作成して破棄します。</span><span class="sxs-lookup"><span data-stu-id="45258-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

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

<span data-ttu-id="45258-297">読み込み中、次のエラー メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="45258-297">Under load, the following error messages are logged:</span></span>

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

<span data-ttu-id="45258-298">インスタンスが`HttpClient`破棄されても、実際のネットワーク接続がオペレーティング システムによって解放されるまでに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="45258-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="45258-299">新しい接続を継続的に作成することで、_ポートの枯渇_が発生します。</span><span class="sxs-lookup"><span data-stu-id="45258-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="45258-300">各クライアント接続には、独自のクライアント ポートが必要です。</span><span class="sxs-lookup"><span data-stu-id="45258-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="45258-301">ポートの枯渇を防ぐ 1 つの方法は、`HttpClient`同じインスタンスを再利用することです。</span><span class="sxs-lookup"><span data-stu-id="45258-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="45258-302">インスタンス`HttpClient`は、アプリが停止すると解放されます。</span><span class="sxs-lookup"><span data-stu-id="45258-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="45258-303">この例では、すべての使い捨てリソースを使用するたびに破棄する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="45258-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="45258-304">`HttpClient`インスタンスの有効期間を処理する方法については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="45258-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="45258-305">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="45258-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="45258-306">HTTP クライアント ファクトリ ブログ</span><span class="sxs-lookup"><span data-stu-id="45258-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="45258-307">オブジェクト プーリング</span><span class="sxs-lookup"><span data-stu-id="45258-307">Object pooling</span></span>

<span data-ttu-id="45258-308">前の例では、インスタンス`HttpClient`を静的にしてすべての要求で再利用する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="45258-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="45258-309">再利用すると、リソースが不足するのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="45258-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="45258-310">オブジェクト プーリング:</span><span class="sxs-lookup"><span data-stu-id="45258-310">Object pooling:</span></span>

* <span data-ttu-id="45258-311">再利用パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="45258-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="45258-312">作成にコストがかかるオブジェクト用に設計されています。</span><span class="sxs-lookup"><span data-stu-id="45258-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="45258-313">プールは、スレッド間で予約および解放できる、事前に初期化されたオブジェクトのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="45258-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="45258-314">プールでは、制限、定義済みのサイズ、成長率などの割り当てルールを定義できます。</span><span class="sxs-lookup"><span data-stu-id="45258-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="45258-315">NuGet パッケージ[Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/)には、このようなプールの管理に役立つクラスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="45258-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="45258-316">次の API エンドポイントは`byte`、要求ごとに乱数で埋め込まれるバッファーをインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="45258-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

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

<span data-ttu-id="45258-317">次のチャートは、中程度の負荷で上記の API を呼び出す表示です。</span><span class="sxs-lookup"><span data-stu-id="45258-317">The following chart display calling the preceding API with moderate load:</span></span>

![先行するグラフ](memory/_static/array.png)

<span data-ttu-id="45258-319">前のグラフでは、ジェネレーション 0 のコレクションは、1 秒に 1 回程度発生します。</span><span class="sxs-lookup"><span data-stu-id="45258-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="45258-320">上記のコードは[、ArrayPool\<T>](xref:System.Buffers.ArrayPool`1)`byte`を使用してバッファーをプールすることで最適化できます。</span><span class="sxs-lookup"><span data-stu-id="45258-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="45258-321">静的インスタンスは、複数の要求にわたって再利用されます。</span><span class="sxs-lookup"><span data-stu-id="45258-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="45258-322">この方法の違いは、プールされたオブジェクトが API から返されるということです。</span><span class="sxs-lookup"><span data-stu-id="45258-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="45258-323">ということは：</span><span class="sxs-lookup"><span data-stu-id="45258-323">That means:</span></span>

* <span data-ttu-id="45258-324">メソッドから戻るとすぐに、オブジェクトが制御不能になります。</span><span class="sxs-lookup"><span data-stu-id="45258-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="45258-325">オブジェクトを解放することはできません。</span><span class="sxs-lookup"><span data-stu-id="45258-325">You can't release the object.</span></span>

<span data-ttu-id="45258-326">オブジェクトの処分を設定するには:</span><span class="sxs-lookup"><span data-stu-id="45258-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="45258-327">破棄可能なオブジェクトにプールされた配列をカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="45258-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="45258-328">プールされたオブジェクトを[Http コンテキスト応答に登録します](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)。</span><span class="sxs-lookup"><span data-stu-id="45258-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="45258-329">`RegisterForDispose`HTTP 要求が完了`Dispose`したときにのみ解放されるように、ターゲット オブジェクトの呼び出しを処理します。</span><span class="sxs-lookup"><span data-stu-id="45258-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

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

<span data-ttu-id="45258-330">プールされていないバージョンと同じ負荷を適用すると、次のグラフが表示されます。</span><span class="sxs-lookup"><span data-stu-id="45258-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![先行するグラフ](memory/_static/pooledarray.png)

<span data-ttu-id="45258-332">主な違いは割り当てられたバイトであり、結果として世代 0 のコレクションの数が大幅に少なくなります。</span><span class="sxs-lookup"><span data-stu-id="45258-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="45258-333">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="45258-333">Additional resources</span></span>

* [<span data-ttu-id="45258-334">ガベージ コレクション</span><span class="sxs-lookup"><span data-stu-id="45258-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="45258-335">同時実行ビジュアライザーを使用したさまざまな GC モードの理解</span><span class="sxs-lookup"><span data-stu-id="45258-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="45258-336">ラージ オブジェクト ヒープが見つけられます</span><span class="sxs-lookup"><span data-stu-id="45258-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="45258-337">ラージ オブジェクト ヒープ</span><span class="sxs-lookup"><span data-stu-id="45258-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
