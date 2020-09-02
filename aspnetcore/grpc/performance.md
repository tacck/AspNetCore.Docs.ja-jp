---
title: ASP.NET Core 向けの gRPC のパフォーマンスに関するベスト プラクティス
author: jamesnk
description: 高パフォーマンスの gRPC サービスを構築するためのベスト プラクティスについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876725"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a>ASP.NET Core 向けの gRPC のパフォーマンスに関するベスト プラクティス

作成者: [James Newton-King](https://twitter.com/jamesnk)

gRPC は、高パフォーマンス サービス向けに設計されています。 このドキュメントでは、gRPC から最大限のパフォーマンスを得る方法について説明します。

## <a name="reuse-channel"></a>チャネルの再利用

gRPC の呼び出しを行う場合は、gRPC チャネルを再利用する必要があります。 チャネルを再利用すると、既存の HTTP/2 接続を介して、呼び出しを多重化できます。

gRPC 呼び出しごとに新しいチャネルが作成された場合、完了までにかかる時間が大幅に増加する可能性があります。 呼び出しのたびに HTTP/2 接続が作成されるため、クライアントとサーバーの間に複数のネットワーク ラウンドトリップが必要になります。

1. ソケットを開く
2. TCP 接続を確立する
3. TLS ネゴシエーションを行う
4. HTTP/2 接続を開始する
5. gRPC 呼び出しを行う

チャネルは、gRPC 呼び出しの間で安全に共有し、再利用できます。

* gRPC クライアントはチャネルを使用して作成されます。 gRPC クライアントは軽量なオブジェクトであり、キャッシュしたり再利用したりする必要はありません。
* 異なる種類のクライアントを含め、1 つチャネルから複数の gRPC クライアントを作成できます。
* チャネルおよびそのチャネルから作成されたクライアントは、複数のスレッドで安全に使用できます。
* チャネルから作成されたクライアントは、複数の同時呼び出しを行えます。

## <a name="connection-concurrency"></a>接続の同時実行

HTTP/2 接続では、通常、1 つの接続で[同時に実行できるストリームの最大数 (アクティブな HTTP 要求数)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) に制限があります。 既定では、ほとんどのサーバーの同時に実行できるストリームの制限数は 100 に設定されます。

gRPC チャネルは 1 つの HTTP/2 接続を使用し、その接続で同時呼び出しが多重化されます。 アクティブな呼び出しの数が接続ストリームの制限に達すると、追加の呼び出しがクライアントのキューに入れられます。 キューに入れられた呼び出しは、アクティブな呼び出しが完了するのを待ってから送信されます。 高負荷のアプリケーションや、長時間実行されるストリーミング gRPC 呼び出しでは、この制限により、呼び出しキューが原因でパフォーマンスの問題が発生する可能性があります。

::: moniker range=">= aspnetcore-5.0"

.NET 5 により、`SocketsHttpHandler.EnableMultipleHttp2Connections` プロパティが導入されています。 `true` に設定すると、同時実行ストリームの制限に達したときに、チャネルによって追加の HTTP/2 接続が作成されます。 `GrpcChannel` が作成されると、その内部の `SocketsHttpHandler` が、追加の HTTP/2 接続を作成するように自動的に構成されます。 アプリによって独自のハンドラーが構成される場合は、`EnableMultipleHttp2Connections` を `true` に設定することを検討してください。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

.NET Core 3.1 アプリには、2 つの回避策があります。

* 高負荷のアプリの領域に対して、別々の gRPC チャネルを作成します。 たとえば、`Logger` gRPC サービスに高い負荷がかかることがあります。 別個のチャネルを使用して、このアプリに `LoggerClient` を作成します。
* gRPC チャネルのプールを使用します。たとえば、gRPC チャネルの一覧を作成します。 `Random` は、gRPC チャネルが必要になるたびに一覧からチャネルを選択するために使用されます。 `Random` を使用すると、複数の接続で呼び出しがランダムに分散されます。

> [!IMPORTANT]
> この問題を解決するもう 1 つの方法は、サーバーの最大同時実行ストリーム制限を増やすことです。 Kestrel では、これは <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> で構成されます。
>
> 最大同時実行ストリーム制限を増やすことはお勧めしません。 1 つの HTTP/2 接続でストリームが多すぎると、パフォーマンスの新たな問題が発生します。
>
> * ストリーム間のスレッド競合により、接続への書き込みが試行されます。
> * 接続パケットが失われると、TCP 層ですべての呼び出しがブロックされます。

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a>キープ アライブ ping

キープ アライブ ping を使用して、非アクティブな状態の間 HTTP/2 接続を維持することができます。 アプリでアクティビティが再開されるときに既存の HTTP/2 接続が準備できていると、接続の再確立による遅延が発生することなく、初期の gRPC 呼び出しをすばやく行うことができます。

キープ アライブ ping は <xref:System.Net.Http.SocketsHttpHandler> に構成されます。

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

上記のコードにより、非アクティブな状態の間 60 秒ごとにキープ アライブ ping をサーバーに送信するチャネルが構成されます。 ping により、サーバーと使用中のすべてのプロキシが、非アクティブが理由で接続が閉じられることがないように保証されます。

::: moniker-end

## <a name="streaming"></a>ストリーム

gRPC 双方向ストリーミングを使用して、高パフォーマンスのシナリオで単項 gRPC 呼び出しを置き換えることができます。 双方向ストリームが開始されると、メッセージのやり取りが、複数の単項 gRPC 呼び出しでメッセージを送信するよりも高速になります。 ストリーム メッセージは、既存の HTTP/2 要求にデータとして送信され、各単項呼び出しに対して新しい HTTP/2 要求を作成するオーバーヘッドがなくなります。

サービスの例:

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

クライアントの例:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

パフォーマンス上の理由で単項呼び出しを双方向ストリーミングに置き換えることは高度な手法であり、多くの状況では適切ではありません。

ストリーミング呼び出しの使用は、次の場合に適しています。

1. 高スループットまたは短い待機時間が必要とされている。
2. gRPC と HTTP/2 が、パフォーマンスのボトルネックとして特定された。
3. クライアントのワーカーが、gRPC サービスを使用して通常のメッセージを送受信している。

単項ではなく、ストリーミング呼び出しを使用する場合は、さらに複雑になり、制限事項が追加されることに注意してください。

1. ストリームは、サービスまたは接続エラーによって中断される可能性があります。 エラーが発生した場合にストリームを再開するためのロジックが必要です。
2. `RequestStream.WriteAsync` は、マルチスレッドでは安全ではありません。 一度に 1 つのストリームに書き込むことができるメッセージは 1 つだけです。 1 つのストリームで複数のスレッドからメッセージを送信するには、メッセージをマーシャリングするための <xref:System.Threading.Channels.Channel%601> のようなプロデューサーまたはコンシューマー キューが必要です。
3. gRPC ストリーミング方式は、1 種類のメッセージの受信と 1 種類のメッセージの送信に制限されます。 たとえば、`rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` により、`RequestMessage` が受信され、`ResponseMessage` が送信されます。 `Any` と `oneof` を使用した不明なメッセージまたは条件付きメッセージに対する Protobuf のサポートにより、この制限を回避できます。
