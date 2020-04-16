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
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>ASP.NETコアのメモリ管理とガベージ コレクション (GC)

[セバスチャン・ロス](https://github.com/sebastienros)と[リック・アンダーソン](https://twitter.com/RickAndMSFT)

メモリ管理は、.NET などのマネージ フレームワークでも複雑です。 メモリの問題の分析と理解は困難な場合があります。 この記事の内容は次のとおりです。

* 多くのメモリ*リーク*と*GC が動作しない*問題によって動機づけられました。 これらの問題のほとんどは、.NET Core でのメモリ消費量のしくみを理解していないか、メモリ使用量の測定方法を理解していなかった場合に発生します。
* 問題のあるメモリの使用を示し、代替アプローチを提案します。

## <a name="how-garbage-collection-gc-works-in-net-core"></a>NET Core でのガベージ コレクション (GC) の動作

GC は、各セグメントが連続したメモリ範囲であるヒープ セグメントを割り当てます。 ヒープに配置されたオブジェクトは、0、1、または 2 の 3 つの世代のいずれかに分類されます。 生成によって、GC がアプリによって参照されなくなったマネージ オブジェクトのメモリ解放を試みる頻度が決まります。 番号が低い世代は GC の方が頻度が高くなります。

オブジェクトは、有効期間に基づいてある世代から別の世代に移動されます。 オブジェクトが長生きするにつれて、オブジェクトはより高い世代に移動されます。 前述のように、上位世代はGCの頻度が低くなります。 短期存続オブジェクトはジェネレーション 0 に常に残ります。 たとえば、Web 要求の存続期間中に参照されるオブジェクトは、有効期間が短い場合です。 アプリケーション レベル[のシングルトン](xref:fundamentals/dependency-injection#service-lifetimes)は、一般にジェネレーション 2 に移行します。

ASP.NET Core アプリが起動すると、GC は次の操作を実行します。

* 初期ヒープ セグメント用にメモリを予約します。
* ランタイムが読み込まれるときに、メモリの一部をコミットします。

上記のメモリ割り当ては、パフォーマンス上の理由から行われます。 パフォーマンスの利点は、連続したメモリ内のヒープ セグメントから得られます。

### <a name="call-gccollect"></a>GC を呼び出します。収集

GC を呼び出しています[。](xref:System.GC.Collect*)明示的に収集する:

* コア アプリの運用ASP.NET実行**しないでください**。
* メモリ リークを調査するときに役立ちます。
* 調査を行う際に、GC がすべてのぶら下がっているオブジェクトをメモリから削除してメモリを測定できることを確認します。

## <a name="analyzing-the-memory-usage-of-an-app"></a>アプリのメモリ使用量の分析

専用ツールは、メモリ使用量の分析に役立ちます。

- オブジェクト参照のカウント
- GC が CPU 使用率に与える影響の測定
- 各世代に使用されるメモリ空間の測定

メモリ使用量を分析するには、次のツールを使用します。

* [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): 生産機で使用できます。
* [Visual Studio デバッガーを使用せずにメモリ使用量を分析する](/visualstudio/profiling/memory-usage-without-debugging2)
* [Visual Studio でのメモリ使用のプロファイリング](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>メモリの問題の検出

タスク マネージャーを使用すると、アプリが使用しているメモリの量ASP.NET把握できます。 タスク マネージャのメモリ値:

* ASP.NET プロセスによって使用されるメモリの量を表します。
* アプリの生きたオブジェクトや、ネイティブメモリ使用量などの他のメモリコンシューマが含まれます。

タスク マネージャーのメモリ値が無限に増加し、フラット化しない場合、アプリはメモリ リークがあります。 次のセクションでは、いくつかのメモリ使用量パターンを示し、説明します。

## <a name="sample-display-memory-usage-app"></a>ディスプレイメモリ使用量アプリのサンプル

[メモリ リーク サンプル アプリ](https://github.com/sebastienros/memoryleak)は GitHub で入手できます。 メモリリークアプリ:

* アプリのリアルタイム メモリと GC データを収集する診断コントローラーが含まれています。
* メモリと GC データを表示するインデックス ページがあります。 インデックス ページは毎秒更新されます。
* さまざまなメモリ ロード パターンを提供する API コントローラが含まれています。
* サポートされていないツールですが、ASP.NET Core アプリのメモリ使用量パターンを表示するために使用できます。

メモリ リークを実行します。 割り当てられたメモリは、GC が発生するまで徐々に増加します。 ツールはデータをキャプチャするカスタム オブジェクトを割り当てるため、メモリが増加します。 次の図は、Gen 0 GC が発生した場合のメモリ リーク インデックス ページを示しています。 API コントローラーから API エンドポイントが呼び出されていないので、グラフは 0 RPS (1 秒あたりの要求数) を示します。

![先行するグラフ](memory/_static/0RPS.png)

このグラフには、メモリ使用量に関する 2 つの値が表示されます。

- 割り当て済み: 管理対象オブジェクトが占有するメモリの量
- [ワーキング セット](/windows/win32/memory/working-set): 物理メモリに現在常駐しているプロセスの仮想アドレス空間内のページのセット。 表示されるワーキング セットは、タスク マネージャが表示する値と同じです。

### <a name="transient-objects"></a>一時的なオブジェクト

次の API は、10 KB の String インスタンスを作成し、それをクライアントに返します。 要求ごとに、新しいオブジェクトがメモリに割り当てられ、応答に書き込まれます。 文字列は UTF-16 文字として .NET に格納されるため、各文字はメモリ内で 2 バイトを使用します。

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

次のグラフは、GC によるメモリ割り当てへの影響を示すために、比較的小さな負荷で生成されます。

![先行するグラフ](memory/_static/bigstring.png)

上記のグラフは、次の項目を示しています。

* 4K RPS(1 秒あたりの要求数)。
* ジェネレーション 0 の GC コレクションは、約 2 秒ごとに発生します。
* ワーキング セットは約 500 MB で一定です。
* CPU は 12% です。
* メモリの消費と放出(GCを介して)は安定しています。

次の図は、マシンで処理できる最大スループットで取得されます。

![先行するグラフ](memory/_static/bigstring2.png)

上記のグラフは、次の項目を示しています。

* 22K RPS
* ジェネレーション 0 の GC コレクションは 1 秒あたりに複数回発生します。
* アプリが 1 秒あたりに大幅に多くのメモリを割り当てたため、ジェネレーション 1 のコレクションがトリガーされます。
* ワーキング セットは約 500 MB で一定です。
* CPU は 33% です。
* メモリの消費と放出(GCを介して)は安定しています。
* CPU (33%)過剰に使用されないため、ガベージ コレクションは多数の割り当てに追いつくことができます。

### <a name="workstation-gc-vs-server-gc"></a>ワークステーション GC 対 サーバー GC

.NET ガベージ コレクタには、2 つの異なるモードがあります。

* **ワークステーションGC:** デスクトップ用に最適化されています。
* **サーバー GC**. ASP.NETコア アプリの既定の GC。 サーバー用に最適化されています。

GC モードは、プロジェクト ファイルまたは公開されたアプリの*runtimeconfig.json*ファイルで明示的に設定できます。 次のマークアップは、`ServerGarbageCollection`プロジェクト ファイルの設定を示しています。

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

プロジェクト`ServerGarbageCollection`ファイルを変更するには、アプリを再構築する必要があります。

**注:** サーバーのガベージ コレクションは、単一コアのマシンでは使用**できません**。 詳細については、「<xref:System.Runtime.GCSettings.IsServerGC>」を参照してください。

次の図は、ワークステーション GC を使用する 5K RPS のメモリ プロファイルを示しています。

![先行するグラフ](memory/_static/workstation.png)

このグラフとサーバーバージョンの違いは、次の点で大きく異なります。

- ワーキング セットは 500 MB から 70 MB に低下します。
- GC は、ジェネレーション 0 のコレクションを 2 秒ごとにではなく、1 秒に複数回収集します。
- GC は 300 MB から 10 MB にドロップします。

一般的な Web サーバー環境では、CPU 使用率はメモリよりも重要であるため、サーバー GC の方が優れています。 メモリ使用率が高く、CPU 使用率が比較的低い場合、ワークステーション GC の方がパフォーマンスが高くなる可能性があります。 たとえば、メモリが不足している複数の Web アプリをホストする高密度です。

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>ドッカーと小さなコンテナを使用したGC

1 台のコンピューターで複数のコンテナー化されたアプリケーションが実行されている場合、ワークステーション GC はサーバー GC よりもプリフォームが多い可能性があります。 詳細については、「[サーバー GC を小さなコンテナーで実行](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/)する」および[「小さなコンテナー シナリオでの Server GC での実行 パート 1 – GC ヒープのハードリミット](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)」を参照してください。

### <a name="persistent-object-references"></a>永続オブジェクト参照

GC は、参照されているオブジェクトを解放できません。 参照されているが不要になったオブジェクトは、メモリ リークを発生させます。 アプリが頻繁にオブジェクトを割り当て、不要になった後にオブジェクトを解放できない場合、時間の経過とともにメモリ使用量が増加します。

次の API は、10 KB の String インスタンスを作成し、それをクライアントに返します。 前の例との違いは、このインスタンスが静的メンバーによって参照されることです。

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

上のコードでは以下の操作が行われます。

* 典型的なメモリ リークの例です。
* 頻繁な呼び出しでは、例外を伴ってプロセスがクラッシュするまで`OutOfMemory`、アプリのメモリが増加します。

![先行するグラフ](memory/_static/eternal.png)

上記のイメージでは、

* エンドポイントの負荷`/api/staticstring`テストにより、メモリが線形に増加します。
* GC は、ジェネレーション 2 のコレクションを呼び出すことによって、メモリの負荷が増大するに応じてメモリを解放しようとします。
* GC は、リークされたメモリを解放できません。 時間と共に割り当て済みおよびワーキング セットが増加します。

キャッシュなどの一部のシナリオでは、メモリの圧迫によって解放されるまで、オブジェクト参照を保持する必要があります。 この<xref:System.WeakReference>クラスは、この種類のキャッシュ コードに使用できます。 `WeakReference`メモリの負荷の下でオブジェクトが収集されます。 の既定の<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>実装では`WeakReference`、 が使用されます。

### <a name="native-memory"></a>ネイティブメモリ

一部の .NET Core オブジェクトは、ネイティブ メモリに依存しています。 ネイティブメモリは、GC では収集**できません**。 ネイティブ メモリを使用する .NET オブジェクトは、ネイティブ コードを使用して解放する必要があります。

.NET は<xref:System.IDisposable>、ネイティブ メモリを解放するためのインターフェイスを提供します。 呼<xref:System.IDisposable.Dispose*>び出されていなくても、[ファイナライザー](/dotnet/csharp/programming-guide/classes-and-structs/destructors)の実行時`Dispose`に正しく実装されたクラスが呼び出されます。

次のコードについて考えてみましょう。

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0)はマネージ クラスであるため、すべてのインスタンスは要求の最後に収集されます。

次の図は、API を継続的に呼`fileprovider`び出している間のメモリ プロファイルを示しています。

![先行するグラフ](memory/_static/fileprovider.png)

上記の表は、メモリ使用量が増加し続けているため、このクラスの実装に関する明白な問題を示しています。 これは、この問題で追跡されている既知の[問題](https://github.com/dotnet/aspnetcore/issues/3110)です。

ユーザー コードで、次のいずれかの方法で同じリークが発生する可能性があります。

* クラスが正しく解放されない。
* 破棄する必要がある依存`Dispose`オブジェクトのメソッドを呼び出す忘れ。

### <a name="large-objects-heap"></a>ラージ オブジェクト ヒープ

メモリの割り当て/解放サイクルが頻繁に発生すると、特にメモリの大きなチャンクを割り当てる場合に、メモリが断片化される可能性があります。 オブジェクトは、連続したメモリ ブロックに割り当てられます。 断片化を軽減するために、GC がメモリを解放すると、最適化を試みます。 このプロセスは**圧縮**と呼ばれます。 圧縮にはオブジェクトの移動が含まれます。 大きなオブジェクトを移動すると、パフォーマンスが低下します。 このため、GC はラージ オブジェクト ヒープ (LOH) と呼ばれる _、ラージ_[オブジェクト](/dotnet/standard/garbage-collection/large-object-heap)用の特別なメモリ ゾーンを作成します。 85,000 バイト (約 83 KB) を超えるオブジェクトは次のとおりです。

* LOHに配置されます。
* 圧縮されません。
* 第 2 世代 GC の間に収集されます。

LOH が満杯になると、GC はジェネレーション 2 のコレクションをトリガーします。 第 2 世代コレクション:

* 本質的に遅いです。
* さらに、他のすべての世代でコレクションをトリガーするコストが発生します。

次のコードは、LOH を直ちに圧縮します。

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

LOH の圧縮については、「」を参照してください<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>。

NET Core 3.0 以降を使用するコンテナーでは、LOH は自動的に圧縮されます。

この動作を示す次の API を示します。

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

次のグラフは、最大負荷の下で`/api/loh/84975`エンドポイントを呼び出すメモリ プロファイルを示しています。

![先行するグラフ](memory/_static/loh1.png)

次の図は、`/api/loh/84976`エンドポイントを呼び出し、もう*1 バイトを*割り当てるメモリ プロファイルを示しています。

![先行するグラフ](memory/_static/loh2.png)

注:`byte[]`この構造体にはオーバーヘッド・バイトがあります。 84,976 バイトが 85,000 の制限をトリガーする理由です。

上記の 2 つのグラフを比較します。

* ワーキング セットは、両方のシナリオで約 450 MB に似ています。
* LOH 要求 (84,975 バイト) の下に、大部分の世代 0 のコレクションが表示されます。
* オーバー LOH 要求は、定数ジェネレーション 2 のコレクションを生成します。 ジェネレーション 2 のコレクションは高価です。 CPU が必要になり、スループットがほぼ 50% 低下します。

一時的な大きなオブジェクトは gen2 GC を引き起こすので特に問題があります。

パフォーマンスを最大限に高めるには、大きなオブジェクトの使用を最小限に抑える必要があります。 可能であれば、大きなオブジェクトを分割します。 たとえば、ASP.NET Core の[応答キャッシュ](xref:performance/caching/response)ミドルウェアは、キャッシュ エントリを 85,000 バイト未満のブロックに分割します。

以下のリンクは、オブジェクトを LOH 制限の下に維持するためのASP.NETコアアプローチを示しています。

* [応答キャッシュ/ストリーム/ストリームユーティリティ.cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [応答キャッシュ/メモリレスポンスキャッシュ.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

詳細については、次を参照してください。

* [ラージ オブジェクト ヒープが見つけられます](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [ラージ オブジェクト ヒープ](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

誤って使用<xref:System.Net.Http.HttpClient>すると、リソース リークが発生する可能性があります。 データベース接続、ソケット、ファイル ハンドルなどのシステム リソース:

* 記憶よりも不足しています。
* メモリよりもリークした場合、より問題があります。

経験豊富な .NET 開発者<xref:System.IDisposable.Dispose*>は、 を<xref:System.IDisposable>実装するオブジェクトを呼び出すことを知っています。 実装`IDisposable`するオブジェクトを破棄しないと、通常はメモリリークやシステム リソースのリークが発生します。

`HttpClient`は`IDisposable`、 を実装しますが、呼び出しのたびに破棄**されるわけではありません**。 むしろ、`HttpClient`再利用する必要があります。

次のエンドポイントは、要求ごとに新`HttpClient`しいインスタンスを作成して破棄します。

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

読み込み中、次のエラー メッセージがログに記録されます。

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

インスタンスが`HttpClient`破棄されても、実際のネットワーク接続がオペレーティング システムによって解放されるまでに時間がかかります。 新しい接続を継続的に作成することで、_ポートの枯渇_が発生します。 各クライアント接続には、独自のクライアント ポートが必要です。

ポートの枯渇を防ぐ 1 つの方法は、`HttpClient`同じインスタンスを再利用することです。

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

インスタンス`HttpClient`は、アプリが停止すると解放されます。 この例では、すべての使い捨てリソースを使用するたびに破棄する必要はありません。

`HttpClient`インスタンスの有効期間を処理する方法については、次を参照してください。

* [HttpClient と有効期間の管理](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [HTTP クライアント ファクトリ ブログ](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>オブジェクト プーリング

前の例では、インスタンス`HttpClient`を静的にしてすべての要求で再利用する方法を示しました。 再利用すると、リソースが不足するのを防ぐことができます。

オブジェクト プーリング:

* 再利用パターンを使用します。
* 作成にコストがかかるオブジェクト用に設計されています。

プールは、スレッド間で予約および解放できる、事前に初期化されたオブジェクトのコレクションです。 プールでは、制限、定義済みのサイズ、成長率などの割り当てルールを定義できます。

NuGet パッケージ[Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/)には、このようなプールの管理に役立つクラスが含まれています。

次の API エンドポイントは`byte`、要求ごとに乱数で埋め込まれるバッファーをインスタンス化します。

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

次のチャートは、中程度の負荷で上記の API を呼び出す表示です。

![先行するグラフ](memory/_static/array.png)

前のグラフでは、ジェネレーション 0 のコレクションは、1 秒に 1 回程度発生します。

上記のコードは[、ArrayPool\<T>](xref:System.Buffers.ArrayPool`1)`byte`を使用してバッファーをプールすることで最適化できます。 静的インスタンスは、複数の要求にわたって再利用されます。

この方法の違いは、プールされたオブジェクトが API から返されるということです。 ということは：

* メソッドから戻るとすぐに、オブジェクトが制御不能になります。
* オブジェクトを解放することはできません。

オブジェクトの処分を設定するには:

* 破棄可能なオブジェクトにプールされた配列をカプセル化します。
* プールされたオブジェクトを[Http コンテキスト応答に登録します](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)。

`RegisterForDispose`HTTP 要求が完了`Dispose`したときにのみ解放されるように、ターゲット オブジェクトの呼び出しを処理します。

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

プールされていないバージョンと同じ負荷を適用すると、次のグラフが表示されます。

![先行するグラフ](memory/_static/pooledarray.png)

主な違いは割り当てられたバイトであり、結果として世代 0 のコレクションの数が大幅に少なくなります。

## <a name="additional-resources"></a>その他のリソース

* [ガベージ コレクション](/dotnet/standard/garbage-collection/)
* [同時実行ビジュアライザーを使用したさまざまな GC モードの理解](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [ラージ オブジェクト ヒープが見つけられます](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [ラージ オブジェクト ヒープ](/dotnet/standard/garbage-collection/large-object-heap)
