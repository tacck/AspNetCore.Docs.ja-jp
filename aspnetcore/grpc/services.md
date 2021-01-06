---
title: gRPC のサービスとメソッドを作成する
author: jamesnk
description: gRPC のサービスとメソッドを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: cc9fc50871cbad1f2ddf63d3c13c3253f24a995b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058741"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="7daae-103">gRPC のサービスとメソッドを作成する</span><span class="sxs-lookup"><span data-stu-id="7daae-103">Create gRPC services and methods</span></span>

<span data-ttu-id="7daae-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7daae-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="7daae-105">このドキュメントでは、C# で gRPC のサービスとメソッドを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="7daae-105">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="7daae-106">取り上げるトピックは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7daae-106">Topics include:</span></span>

* <span data-ttu-id="7daae-107">*.proto* ファイルでサービスとメソッドを定義する方法。</span><span class="sxs-lookup"><span data-stu-id="7daae-107">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="7daae-108">gRPC の C# ツールを使用して生成されるコード。</span><span class="sxs-lookup"><span data-stu-id="7daae-108">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="7daae-109">gRPC のサービスとメソッドの実装。</span><span class="sxs-lookup"><span data-stu-id="7daae-109">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="7daae-110">新しい gRPC サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="7daae-110">Create new gRPC services</span></span>

<span data-ttu-id="7daae-111">[C# での gRPC サービス](xref:grpc/basics)では、API 開発に対する gRPC のコントラクト優先アプローチが導入されました。</span><span class="sxs-lookup"><span data-stu-id="7daae-111">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="7daae-112">サービスとメッセージは、 *.proto* ファイル内で定義されます。</span><span class="sxs-lookup"><span data-stu-id="7daae-112">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="7daae-113">これで、C# ツールによって *.proto* ファイルからコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="7daae-113">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="7daae-114">サーバー側アセットでは、サービスごとの抽象基本データ型と、すべてのメッセージのクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="7daae-114">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="7daae-115">次の *.proto* ファイルは:</span><span class="sxs-lookup"><span data-stu-id="7daae-115">The following *.proto* file:</span></span>

* <span data-ttu-id="7daae-116">`Greeter` サービスを定義します。</span><span class="sxs-lookup"><span data-stu-id="7daae-116">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="7daae-117">`Greeter` サービスで `SayHello` 呼び出しを定義します。</span><span class="sxs-lookup"><span data-stu-id="7daae-117">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="7daae-118">`SayHello` では、`HelloRequest` メッセージを送信し、`HelloReply` メッセージを受信します</span><span class="sxs-lookup"><span data-stu-id="7daae-118">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

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

<span data-ttu-id="7daae-119">C# ツールによって C# の `GreeterBase` 基本データ型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="7daae-119">C# tooling generates the C# `GreeterBase` base type:</span></span>

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

<span data-ttu-id="7daae-120">既定では、生成された `GreeterBase` は何も行いません。</span><span class="sxs-lookup"><span data-stu-id="7daae-120">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="7daae-121">その仮想メソッド `SayHello` は、それを呼び出すすべてのクライアントに `UNIMPLEMENTED` エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="7daae-121">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="7daae-122">サービスが役に立つようにするには、アプリで `GreeterBase` の具象実装を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7daae-122">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="7daae-123">サービスの実装は、アプリに登録されます。</span><span class="sxs-lookup"><span data-stu-id="7daae-123">The service implementation is registered with the app.</span></span> <span data-ttu-id="7daae-124">ASP.NET Core の gRPC によってホストされているサービスは、`MapGrpcService` メソッドを使用してルーティングパイプラインに追加される必要があります。</span><span class="sxs-lookup"><span data-stu-id="7daae-124">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="7daae-125">詳細については、「 <xref:grpc/aspnetcore> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7daae-125">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="7daae-126">gRPC のメソッドを実装する</span><span class="sxs-lookup"><span data-stu-id="7daae-126">Implement gRPC methods</span></span>

<span data-ttu-id="7daae-127">gRPC サービスには、さまざまな種類のメソッドを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="7daae-127">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="7daae-128">サービスによってメッセージがどのように送受信されるかは、定義されているメソッドの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="7daae-128">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="7daae-129">gRPC のメソッドの種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7daae-129">The gRPC method types are:</span></span>

* <span data-ttu-id="7daae-130">単項</span><span class="sxs-lookup"><span data-stu-id="7daae-130">Unary</span></span>
* <span data-ttu-id="7daae-131">サーバー ストリーミング。</span><span class="sxs-lookup"><span data-stu-id="7daae-131">Server streaming</span></span>
* <span data-ttu-id="7daae-132">クライアント ストリーミング</span><span class="sxs-lookup"><span data-stu-id="7daae-132">Client streaming</span></span>
* <span data-ttu-id="7daae-133">双方向ストリーミング</span><span class="sxs-lookup"><span data-stu-id="7daae-133">Bi-directional streaming</span></span>

<span data-ttu-id="7daae-134">ストリーミング呼び出しは、 *.proto* ファイルの `stream` キーワードで指定します。</span><span class="sxs-lookup"><span data-stu-id="7daae-134">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="7daae-135">`stream` は、呼び出しの要求メッセージ、応答メッセージ、またはその両方に配置できます。</span><span class="sxs-lookup"><span data-stu-id="7daae-135">`stream` can be placed on a call's request message, response message, or both.</span></span>

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

<span data-ttu-id="7daae-136">各呼び出し型には、異なるメソッド シグネチャがあります。</span><span class="sxs-lookup"><span data-stu-id="7daae-136">Each call type has a different method signature.</span></span> <span data-ttu-id="7daae-137">具象実装で抽象基本サービス型から生成されたメソッドをオーバーライドすると、正しい引数と戻り値の型が使用されるようになります。</span><span class="sxs-lookup"><span data-stu-id="7daae-137">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="7daae-138">単項メソッド</span><span class="sxs-lookup"><span data-stu-id="7daae-138">Unary method</span></span>

<span data-ttu-id="7daae-139">単項メソッドは、要求メッセージをパラメーターとして取得し、応答を返します。</span><span class="sxs-lookup"><span data-stu-id="7daae-139">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="7daae-140">単項呼び出しは、応答が返されたときに完了します。</span><span class="sxs-lookup"><span data-stu-id="7daae-140">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="7daae-141">単項呼び出しは、[Web API コントローラーでのアクション](xref:web-api/index)に最もよく似ています。</span><span class="sxs-lookup"><span data-stu-id="7daae-141">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="7daae-142">gRPC のメソッドとアクションとの重要な相違点の 1 つとして、gRPC のメソッドは要求の一部を別のメソッド引数にバインドできない点があります。</span><span class="sxs-lookup"><span data-stu-id="7daae-142">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="7daae-143">gRPC のメソッドには、受信要求データに対して常に 1 つのメッセージ引数があります。</span><span class="sxs-lookup"><span data-stu-id="7daae-143">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="7daae-144">複数の値も、要求メッセージでフィールドにすることで、gRPC サービスに送信できます。</span><span class="sxs-lookup"><span data-stu-id="7daae-144">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="7daae-145">サーバー ストリーミング メソッド</span><span class="sxs-lookup"><span data-stu-id="7daae-145">Server streaming method</span></span>

<span data-ttu-id="7daae-146">サーバー ストリーミング メソッドは、要求メッセージをパラメーターとして取得します。</span><span class="sxs-lookup"><span data-stu-id="7daae-146">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="7daae-147">複数のメッセージは呼び出し元にストリーミングで戻すことができるため、応答メッセージの送信には `responseStream.WriteAsync` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="7daae-147">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="7daae-148">メソッドが返されると、サーバー ストリーミングの呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="7daae-148">A server streaming call is complete when the method returns.</span></span>

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

<span data-ttu-id="7daae-149">クライアントは、サーバー ストリーミング メソッドが開始された後に、追加のメッセージやデータを送信することはできません。</span><span class="sxs-lookup"><span data-stu-id="7daae-149">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="7daae-150">一部のストリーミング メソッドは、永久に実行するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="7daae-150">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="7daae-151">連続ストリーミング メソッドの場合、クライアントは不要になった呼び出しをキャンセルできます。</span><span class="sxs-lookup"><span data-stu-id="7daae-151">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="7daae-152">キャンセルが発生すると、クライアントはサーバーにシグナルを送信し、[ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) が発生します。</span><span class="sxs-lookup"><span data-stu-id="7daae-152">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="7daae-153">`CancellationToken` トークンは、次を目的とする場合に非同期メソッドを使用してサーバーで使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7daae-153">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="7daae-154">すべての非同期処理を、ストリーミング呼び出しと共にキャンセルする。</span><span class="sxs-lookup"><span data-stu-id="7daae-154">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="7daae-155">メソッドをすぐに終了する。</span><span class="sxs-lookup"><span data-stu-id="7daae-155">The method exits quickly.</span></span>

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

### <a name="client-streaming-method"></a><span data-ttu-id="7daae-156">クライアント ストリーミング メソッド</span><span class="sxs-lookup"><span data-stu-id="7daae-156">Client streaming method</span></span>

<span data-ttu-id="7daae-157">クライアント ストリーミング メソッドは、メッセージを受信するメソッド "*なしで*" 開始します。</span><span class="sxs-lookup"><span data-stu-id="7daae-157">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="7daae-158">`requestStream` パラメーターは、クライアントからメッセージを読み取るために使用されます。</span><span class="sxs-lookup"><span data-stu-id="7daae-158">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="7daae-159">応答メッセージが返されると、クライアント ストリーミングの呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="7daae-159">A client streaming call is complete when a response message is returned:</span></span>

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

<span data-ttu-id="7daae-160">C# 8 以降を使用している場合は、`await foreach` 構文を使用してメッセージを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="7daae-160">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="7daae-161">`IAsyncStreamReader<T>.ReadAllAsync()` 拡張メソッドは、要求ストリームからすべてのメッセージを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="7daae-161">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

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

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="7daae-162">双方向ストリーミング メソッド</span><span class="sxs-lookup"><span data-stu-id="7daae-162">Bi-directional streaming method</span></span>

<span data-ttu-id="7daae-163">双方向ストリーミング メソッドは、メッセージを受信するメソッド "*なしで*" 開始します。</span><span class="sxs-lookup"><span data-stu-id="7daae-163">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="7daae-164">`requestStream` パラメーターは、クライアントからメッセージを読み取るために使用されます。</span><span class="sxs-lookup"><span data-stu-id="7daae-164">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="7daae-165">このメソッドでは、`responseStream.WriteAsync` を使用して、メッセージを送信することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="7daae-165">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="7daae-166">双方向ストリーミング呼び出しは、メソッドが返されると完了します。</span><span class="sxs-lookup"><span data-stu-id="7daae-166">A bi-directional streaming call is complete when the the method returns:</span></span>

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

<span data-ttu-id="7daae-167">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="7daae-167">The preceding code:</span></span>

* <span data-ttu-id="7daae-168">要求ごとに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="7daae-168">Sends a response for each request.</span></span>
* <span data-ttu-id="7daae-169">双方向ストリーミングの基本的な使用方法です。</span><span class="sxs-lookup"><span data-stu-id="7daae-169">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="7daae-170">要求の読み取り、応答の同時送信など、より複雑なシナリオをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="7daae-170">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

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

<span data-ttu-id="7daae-171">双方向ストリーミング メソッドでは、クライアントとサービスはいつでも相互にメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="7daae-171">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="7daae-172">双方向メソッドの最適な実装は、要件によって異なります。</span><span class="sxs-lookup"><span data-stu-id="7daae-172">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="7daae-173">gRPC 要求ヘッダーにアクセスする</span><span class="sxs-lookup"><span data-stu-id="7daae-173">Access gRPC request headers</span></span>

<span data-ttu-id="7daae-174">要求メッセージは、クライアントが gRPC サービスにデータを送信する唯一の方法ではありません。</span><span class="sxs-lookup"><span data-stu-id="7daae-174">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="7daae-175">ヘッダー値は、`ServerCallContext.RequestHeaders` を使用してサービスで使用できます。</span><span class="sxs-lookup"><span data-stu-id="7daae-175">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="7daae-176">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7daae-176">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
