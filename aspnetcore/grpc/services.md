---
title: gRPC のサービスとメソッドを作成する
author: jamesnk
description: gRPC のサービスとメソッドを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: fde589b2de9d908db26a2557c5f57c715625aadc
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945483"
---
# <a name="create-grpc-services-and-methods"></a>gRPC のサービスとメソッドを作成する

このドキュメントでは、C# で gRPC のサービスとメソッドを作成する方法について説明します。 取り上げるトピックは次のとおりです。

* *.proto* ファイルでサービスとメソッドを定義する方法。
* gRPC の C# ツールを使用して生成されるコード。
* gRPC のサービスとメソッドの実装。

## <a name="create-new-grpc-services"></a>新しい gRPC サービスを作成する

[C# での gRPC サービス](xref:grpc/basics)では、API 開発に対する gRPC のコントラクト優先アプローチが導入されました。 サービスとメッセージは、 *.proto* ファイル内で定義されます。 これで、C# ツールによって *.proto* ファイルからコードが生成されます。 サーバー側アセットでは、サービスごとの抽象基本データ型と、すべてのメッセージのクラスが生成されます。

次の *.proto* ファイルは:

* `Greeter` サービスを定義します。
* `Greeter` サービスで `SayHello` 呼び出しを定義します。
* `SayHello` では、`HelloRequest` メッセージを送信し、`HelloReply` メッセージを受信します

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

C# ツールによって C# の `GreeterBase` 基本データ型が生成されます。

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

既定では、生成された `GreeterBase` は何も行いません。 その仮想メソッド `SayHello` は、それを呼び出すすべてのクライアントに `UNIMPLEMENTED` エラーを返します。 サービスが役に立つようにするには、アプリで `GreeterBase` の具象実装を作成する必要があります。

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

サービスの実装は、アプリに登録されます。 ASP.NET Core の gRPC によってホストされているサービスは、`MapGrpcService` メソッドを使用してルーティングパイプラインに追加される必要があります。

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

詳細については、「 <xref:grpc/aspnetcore> 」を参照してください。

## <a name="implement-grpc-methods"></a>gRPC のメソッドを実装する

gRPC サービスには、さまざまな種類のメソッドを含めることができます。 サービスによってメッセージがどのように送受信されるかは、定義されているメソッドの種類によって異なります。 gRPC のメソッドの種類は次のとおりです。

* 単項
* サーバー ストリーミング。
* クライアント ストリーミング
* 双方向ストリーミング

ストリーミング呼び出しは、 *.proto* ファイルの `stream` キーワードで指定します。 `stream` は、呼び出しの要求メッセージ、応答メッセージ、またはその両方に配置できます。

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

各呼び出し型には、異なるメソッド シグネチャがあります。 具象実装で抽象基本サービス型から生成されたメソッドをオーバーライドすると、正しい引数と戻り値の型が使用されるようになります。

### <a name="unary-method"></a>単項メソッド

単項メソッドは、要求メッセージをパラメーターとして取得し、応答を返します。 単項呼び出しは、応答が返されたときに完了します。

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

単項呼び出しは、[Web API コントローラーでのアクション](xref:web-api/index)に最もよく似ています。 gRPC のメソッドとアクションとの重要な相違点の 1 つとして、gRPC のメソッドは要求の一部を別のメソッド引数にバインドできない点があります。 gRPC のメソッドには、受信要求データに対して常に 1 つのメッセージ引数があります。 複数の値も、要求メッセージでフィールドにすることで、gRPC サービスに送信できます。

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>サーバー ストリーミング メソッド

サーバー ストリーミング メソッドは、要求メッセージをパラメーターとして取得します。 複数のメッセージは呼び出し元にストリーミングで戻すことができるため、応答メッセージの送信には `responseStream.WriteAsync` が使用されます。 メソッドが返されると、サーバー ストリーミングの呼び出しが完了します。

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

クライアントは、サーバー ストリーミング メソッドが開始された後に、追加のメッセージやデータを送信することはできません。 一部のストリーミング メソッドは、永久に実行するように設計されています。 連続ストリーミング メソッドの場合、クライアントは不要になった呼び出しをキャンセルできます。 キャンセルが発生すると、クライアントはサーバーにシグナルを送信し、[ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) が発生します。 `CancellationToken` トークンは、次を目的とする場合に非同期メソッドを使用してサーバーで使用する必要があります。

* すべての非同期処理を、ストリーミング呼び出しと共にキャンセルする。
* メソッドをすぐに終了する。

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a>クライアント ストリーミング メソッド

クライアント ストリーミング メソッドは、メッセージを受信するメソッド "*なしで*" 開始します。 `requestStream` パラメーターは、クライアントからメッセージを読み取るために使用されます。 応答メッセージが返されると、クライアント ストリーミングの呼び出しが完了します。

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

C# 8 以降を使用している場合は、`await foreach` 構文を使用してメッセージを読み取ることができます。 `IAsyncStreamReader<T>.ReadAllAsync()` 拡張メソッドは、要求ストリームからすべてのメッセージを読み取ります。

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a>双方向ストリーミング メソッド

双方向ストリーミング メソッドは、メッセージを受信するメソッド "*なしで*" 開始します。 `requestStream` パラメーターは、クライアントからメッセージを読み取るために使用されます。 このメソッドでは、`responseStream.WriteAsync` を使用して、メッセージを送信することを選択できます。 双方向ストリーミング呼び出しは、メソッドが返されると完了します。

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

上記のコードでは次の操作が行われます。

* 要求ごとに応答を送信します。
* 双方向ストリーミングの基本的な使用方法です。

要求の読み取り、応答の同時送信など、より複雑なシナリオをサポートできます。

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

双方向ストリーミング メソッドでは、クライアントとサービスはいつでも相互にメッセージを送信できます。 双方向メソッドの最適な実装は、要件によって異なります。

## <a name="access-grpc-request-headers"></a>gRPC 要求ヘッダーにアクセスする

要求メッセージは、クライアントが gRPC サービスにデータを送信する唯一の方法ではありません。 ヘッダー値は、`ServerCallContext.RequestHeaders` を使用してサービスで使用できます。

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/basics>
* <xref:grpc/client>
