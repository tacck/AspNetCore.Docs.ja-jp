---
title: ASP.NET Core でストリーミングを使用するSignalR
author: bradygaster
description: クライアントとサーバーの間でデータをストリーミングする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/streaming
ms.openlocfilehash: e0eabe711fd69e42bd9bfa5e03a92e1df780e4db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022512"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a>ASP.NET Core でストリーミングを使用するSignalR

[Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR は、クライアントからサーバー、およびサーバーからクライアントへのストリーミングをサポートします。 これは、時間の経過と共にデータのフラグメントが到着するシナリオに役立ちます。 ストリーミング時には、すべてのデータが使用可能になるのを待機するのではなく、使用可能になるとすぐに各フラグメントがクライアントまたはサーバーに送信されます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR は、サーバーメソッドのストリーミング戻り値をサポートします。 これは、時間の経過と共にデータのフラグメントが到着するシナリオに役立ちます。 戻り値がクライアントにストリーミングされると、すべてのデータが使用可能になるまで待機するのではなく、使用可能になるとすぐに各フラグメントがクライアントに送信されます。

::: moniker-end

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="set-up-a-hub-for-streaming"></a>ストリーミング用のハブを設定する

::: moniker range=">= aspnetcore-3.0"

ハブメソッドは、、、 <xref:System.Collections.Generic.IAsyncEnumerable`1> 、またはを返すと、自動的にストリーミングハブメソッドになり <xref:System.Threading.Channels.ChannelReader%601> `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` ます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ハブメソッドは、またはを返すと、自動的にストリーミングハブメソッドになり <xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>` ます。

::: moniker-end

### <a name="server-to-client-streaming"></a>サーバーからクライアントへのストリーミング

::: moniker range=">= aspnetcore-3.0"

ストリーミングハブのメソッドは `IAsyncEnumerable<T>` 、に加えてを返すことができ `ChannelReader<T>` ます。 を返す最も簡単 `IAsyncEnumerable<T>` な方法は、次の例に示すように、ハブメソッドを非同期反復子メソッドにすることです。 ハブの非同期反復子メソッドは、 `CancellationToken` クライアントがストリームからアンサブスクライブしたときにトリガーされるパラメーターを受け取ることができます。 非同期反復子メソッドは、チャネルに共通する問題を回避します。たとえば、を完了しないうちに十分な初期状態を返さない場合 `ChannelReader` や、メソッドを終了する場合など <xref:System.Threading.Channels.ChannelWriter`1> です。

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

次のサンプルは、チャネルを使用してクライアントにデータをストリーミングする方法の基本を示しています。 オブジェクトがに書き込まれるたびに、 <xref:System.Threading.Channels.ChannelWriter%601> オブジェクトは直ちにクライアントに送信されます。 最後に、は、 `ChannelWriter` ストリームが閉じられていることをクライアントに通知するために完了します。

> [!NOTE]
> `ChannelWriter<T>`バックグラウンドスレッドでに書き込み、できるだけ早くを返し `ChannelReader` ます。 が返されるまで、他のハブ呼び出しはブロックされ `ChannelReader` ます。
>
> でロジックをラップ `try ... catch` します。 `Channel` `catch` `catch` ハブメソッドの呼び出しが正しく完了していることを確認するには、のとの外側にあるを完了します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

サーバーからクライアントへのストリーミングハブメソッドは、 `CancellationToken` クライアントがストリームからアンサブスクライブしたときにトリガーされるパラメーターを受け取ることができます。 ストリームの終了前にクライアントが切断された場合は、このトークンを使用してサーバー操作を停止し、リソースを解放します。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>クライアントとサーバー間のストリーミング

ハブメソッドは、型または型の1つ以上のオブジェクトを受け入れると、クライアントとサーバー間のストリーミングハブメソッドに自動的になり <xref:System.Threading.Channels.ChannelReader%601> <xref:System.Collections.Generic.IAsyncEnumerable%601> ます。 次のサンプルは、クライアントから送信されたストリーミングデータの読み取りの基本を示しています。 クライアントがに書き込むたびに、 <xref:System.Threading.Channels.ChannelWriter%601> データは `ChannelReader` ハブメソッドの読み取り元のサーバー上のに書き込まれます。

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<xref:System.Collections.Generic.IAsyncEnumerable%601>メソッドのバージョンは次のとおりです。

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a>.NET クライアント

### <a name="server-to-client-streaming"></a>サーバーからクライアントへのストリーミング


::: moniker range=">= aspnetcore-3.0"

`StreamAsync` `StreamAsChannelAsync` のメソッドとメソッド `HubConnection` は、サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。 ハブメソッドで定義されているハブメソッド名と引数を `StreamAsync` またはに渡し `StreamAsChannelAsync` ます。 およびのジェネリックパラメーターは、 `StreamAsync<T>` `StreamAsChannelAsync<T>` ストリーミングメソッドによって返されるオブジェクトの型を指定します。 または型の `IAsyncEnumerable<T>` オブジェクト `ChannelReader<T>` がストリーム呼び出しから返され、クライアントのストリームを表します。

`StreamAsync`を返す例を `IAsyncEnumerable<int>` 次に示します。

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

`StreamAsChannelAsync`を返す対応する例を `ChannelReader<int>` 次に示します。

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

`StreamAsChannelAsync`のメソッドは、 `HubConnection` サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。 ハブメソッドで定義されているハブメソッド名と引数をに渡し `StreamAsChannelAsync` ます。 のジェネリックパラメーターは、 `StreamAsChannelAsync<T>` ストリーミングメソッドによって返されるオブジェクトの型を指定します。 は `ChannelReader<T>` ストリーム呼び出しから返され、クライアントのストリームを表します。

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`StreamAsChannelAsync`のメソッドは、 `HubConnection` サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。 ハブメソッドで定義されているハブメソッド名と引数をに渡し `StreamAsChannelAsync` ます。 のジェネリックパラメーターは、 `StreamAsChannelAsync<T>` ストリーミングメソッドによって返されるオブジェクトの型を指定します。 は `ChannelReader<T>` ストリーム呼び出しから返され、クライアントのストリームを表します。

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>クライアントとサーバー間のストリーミング

.NET クライアントからクライアントとサーバー間のストリーミングハブメソッドを呼び出すには、2つの方法があります。 `IAsyncEnumerable<T>` `ChannelReader` `SendAsync` `InvokeAsync` `StreamAsChannelAsync` 呼び出されたハブメソッドに応じて、またはを引数として、、またはに渡すことができます。

オブジェクトまたはオブジェクトにデータが書き込まれるたびに `IAsyncEnumerable` `ChannelWriter` 、サーバーのハブメソッドは、クライアントからのデータを使用して新しい項目を受け取ります。

オブジェクトを使用している場合 `IAsyncEnumerable` 、ストリームは、ストリーム項目を返すメソッドが終了した後に終了します。

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

または、を使用している場合は、 `ChannelWriter` 次のようにチャネルを完成させ `channel.Writer.Complete()` ます。

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>JavaScript クライアント

### <a name="server-to-client-streaming"></a>サーバーからクライアントへのストリーミング

JavaScript クライアントは、を使用してハブでサーバーからクライアントへのストリーミングメソッドを呼び出し `connection.stream` ます。 メソッドは、 `stream` 次の2つの引数を受け取ります。

* ハブメソッドの名前。 次の例では、ハブメソッド名は `Counter` です。
* ハブメソッドで定義されている引数。 次の例では、引数は、受信するストリーム項目数とストリーム項目間の遅延をカウントします。

`connection.stream``IStreamResult`メソッドを含むを返し `subscribe` ます。 をに渡し、、、およびの各 `IStreamSubscriber` `subscribe` コールバックを設定して、 `next` `error` `complete` 呼び出しから通知を受信し `stream` ます。

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

クライアントからストリームを終了するには、 `dispose` メソッドから返されたに対してメソッドを呼び出し `ISubscription` `subscribe` ます。 このメソッドを呼び出すと、 `CancellationToken` ハブメソッドのパラメーターがキャンセルされます (指定した場合)。

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

クライアントからストリームを終了するには、 `dispose` メソッドから返されたに対してメソッドを呼び出し `ISubscription` `subscribe` ます。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>クライアントとサーバー間のストリーミング

JavaScript クライアントは、 `Subject` `send` `invoke` `stream` 呼び出されたハブメソッドに応じて、を引数として、、またはに渡すことによって、ハブでクライアントからサーバーへのストリーミングメソッドを呼び出します。 は、の `Subject` ように見えるクラスです `Subject` 。 たとえば、RxJS では、そのライブラリの[サブジェクト](https://rxjs-dev.firebaseapp.com/api/index/class/Subject)クラスを使用できます。

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

項目を使用してを呼び出す `subject.next(item)` と、項目がストリームに書き込まれ、ハブメソッドはサーバー上の項目を受け取ります。

ストリームを終了するには、を呼び出し `subject.complete()` ます。

## <a name="java-client"></a>Java クライアント

### <a name="server-to-client-streaming"></a>サーバーからクライアントへのストリーミング

Java クライアントは、メソッドを使用して SignalR `stream` ストリーミングメソッドを呼び出します。 `stream`3つ以上の引数を受け取ります。

* ストリーム項目の予期される型。
* ハブメソッドの名前。
* ハブメソッドで定義されている引数。

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`stream`のメソッドは、 `HubConnection` ストリーム項目の型の観測可能なを返します。 観測可能な型の `subscribe` メソッドは `onNext` 、、 `onError` および `onCompleted` ハンドラーが定義されている場所です。

::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* [取って代わり](xref:signalr/hubs)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
