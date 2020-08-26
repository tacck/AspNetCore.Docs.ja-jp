---
title: .NET クライアントを使用して gRPC サービスを呼び出す
author: jamesnk
description: .NET gRPC クライアントを使用して gRPC サービスを呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 28e4f372e301a673644bfa97763ebc930f2d0ad5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634333"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="dc688-103">.NET クライアントを使用して gRPC サービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="dc688-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="dc688-104">.NET gRPC クライアント ライブラリは、[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet パッケージで提供されています。</span><span class="sxs-lookup"><span data-stu-id="dc688-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="dc688-105">このドキュメントでは、以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dc688-105">This document explains how to:</span></span>

* <span data-ttu-id="dc688-106">gRPC サービスを呼び出すように gRPC クライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="dc688-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="dc688-107">unary、サーバー ストリーミング、クライアント ストリーミング、双方向ストリーミングの各メソッドに対する gRPC 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="dc688-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="dc688-108">gRPC クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="dc688-108">Configure gRPC client</span></span>

<span data-ttu-id="dc688-109">gRPC クライアントは、[ *\*.proto* ファイルから生成される](xref:grpc/basics#generated-c-assets)具象的なクライアントの種類です。</span><span class="sxs-lookup"><span data-stu-id="dc688-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="dc688-110">具象 gRPC クライアントには、 *\*.proto* ファイル内の gRPC サービスに変換するためのメソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc688-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="dc688-111">gRPC クライアントはチャネルから作成されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="dc688-112">まず `GrpcChannel.ForAddress` を使用してチャネルを作成し、次にそのチャネルを使用して、gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc688-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="dc688-113">チャネルは、gRPC サービスへの有効期限が長い接続を表します。</span><span class="sxs-lookup"><span data-stu-id="dc688-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="dc688-114">チャネルが作成されるときには、サービスの呼び出しに関連するオプションによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="dc688-115">たとえば呼び出しを行うために使用される `HttpClient` の場合、`GrpcChannelOptions` で送受信メッセージの最大サイズとログ記録を指定し、`GrpcChannel.ForAddress` と共に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="dc688-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="dc688-116">オプションの完全な一覧については、[クライアント構成のオプション](xref:grpc/configuration#configure-client-options)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dc688-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="dc688-117">TLS の構成</span><span class="sxs-lookup"><span data-stu-id="dc688-117">Configure TLS</span></span>

<span data-ttu-id="dc688-118">gRPC クライアントでは、呼び出されたサービスと同じ接続レベルのセキュリティを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dc688-118">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="dc688-119">gRPC クライアントのトランスポート層セキュリティ (TLS) は、gRPC チャネルの作成時に構成されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-119">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="dc688-120">gRPC クライアントによってサービスが呼び出され、そのチャネルとサービスの接続レベルのセキュリティが一致しなかった場合、エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="dc688-120">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="dc688-121">TLS を使用するように gRPC チャネルを構成するには、サーバー アドレスが `https` で始まることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dc688-121">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="dc688-122">たとえば、`GrpcChannel.ForAddress("https://localhost:5001")` では HTTPS プロトコルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-122">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="dc688-123">gRPC チャネルでは、TLS によってセキュリティで保護された接続が自動的にネゴシエートされ、安全な接続を使用して gRPC 呼び出しが行われます。</span><span class="sxs-lookup"><span data-stu-id="dc688-123">The gRPC channel automatically negotates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="dc688-124">gRPC では、TLS 経由のクライアント証明書認証がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="dc688-124">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="dc688-125">gRPC チャネルを使用したクライアント証明書の構成について詳しくは、<xref:grpc/authn-and-authz#client-certificate-authentication> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dc688-125">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="dc688-126">セキュリティで保護されていない gRPC サービスを呼び出すには、サーバー アドレスが `http` で始まることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dc688-126">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="dc688-127">たとえば、`GrpcChannel.ForAddress("http://localhost:5000")` では HTTP プロトコルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-127">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="dc688-128">.NET Core 3.1 以降では、[.NET クライアントで安全でない gRPC サービスを呼び出す](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)場合、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="dc688-128">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="dc688-129">クライアント パフォーマンス</span><span class="sxs-lookup"><span data-stu-id="dc688-129">Client performance</span></span>

<span data-ttu-id="dc688-130">チャネルおよびクライアントのパフォーマンスと使用状況:</span><span class="sxs-lookup"><span data-stu-id="dc688-130">Channel and client performance and usage:</span></span>

* <span data-ttu-id="dc688-131">チャネルの作成は、コストのかかる操作となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc688-131">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="dc688-132">チャネルを gRPC 呼び出しのために再利用すると、パフォーマンス上のメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="dc688-132">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="dc688-133">gRPC クライアントはチャネルを使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-133">gRPC clients are created with channels.</span></span> <span data-ttu-id="dc688-134">gRPC クライアントは軽量なオブジェクトであり、キャッシュしたり再利用したりする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dc688-134">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="dc688-135">異なる種類のクライアントを含め、1 つチャネルから複数の gRPC クライアントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="dc688-135">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="dc688-136">チャネルおよびそのチャネルから作成されたクライアントは、複数のスレッドで安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc688-136">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="dc688-137">チャネルから作成されたクライアントは、複数の同時呼び出しを行えます。</span><span class="sxs-lookup"><span data-stu-id="dc688-137">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="dc688-138">`GrpcChannel.ForAddress` は、gRPC クライアントを作成する際の唯一のオプションではありません。</span><span class="sxs-lookup"><span data-stu-id="dc688-138">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="dc688-139">ASP.NET Core アプリから gRPC サービスを呼び出そうとしている場合は、[gRPC クライアント ファクトリの統合](xref:grpc/clientfactory)を検討してください。</span><span class="sxs-lookup"><span data-stu-id="dc688-139">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="dc688-140">gRPC と `HttpClientFactory` の統合により、gRPC クライアントを一元的に作成する別の方法が得られます。</span><span class="sxs-lookup"><span data-stu-id="dc688-140">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="dc688-141">現在のところ、`Grpc.Net.Client` による HTTP/2 を介した gRPC の呼び出しは、Xamarin ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="dc688-141">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="dc688-142">将来の Xamarin のリリースで HTTP/2 のサポートを向上させるために作業を進めています。</span><span class="sxs-lookup"><span data-stu-id="dc688-142">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="dc688-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) と [gRPC-Web](xref:grpc/browser) は、現在でも機能する実行可能な代替手段です。</span><span class="sxs-lookup"><span data-stu-id="dc688-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="dc688-144">gRPC 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="dc688-144">Make gRPC calls</span></span>

<span data-ttu-id="dc688-145">gRPC 呼び出しは、クライアントでメソッドを呼び出すことで開始されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-145">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="dc688-146">gRPC クライアントは、メッセージのシリアル化と、gRPC 呼び出しの正しいサービスへのアドレス指定を処理します。</span><span class="sxs-lookup"><span data-stu-id="dc688-146">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="dc688-147">gRPC には、さまざまな種類のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="dc688-147">gRPC has different types of methods.</span></span> <span data-ttu-id="dc688-148">クライアントを使用して gRPC 呼び出しを行う方法は、呼び出そうとしているメソッドの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="dc688-148">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="dc688-149">gRPC のメソッドの種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dc688-149">The gRPC method types are:</span></span>

* <span data-ttu-id="dc688-150">単項</span><span class="sxs-lookup"><span data-stu-id="dc688-150">Unary</span></span>
* <span data-ttu-id="dc688-151">サーバー ストリーミング。</span><span class="sxs-lookup"><span data-stu-id="dc688-151">Server streaming</span></span>
* <span data-ttu-id="dc688-152">クライアント ストリーミング</span><span class="sxs-lookup"><span data-stu-id="dc688-152">Client streaming</span></span>
* <span data-ttu-id="dc688-153">双方向ストリーミング</span><span class="sxs-lookup"><span data-stu-id="dc688-153">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="dc688-154">Unary 呼び出し</span><span class="sxs-lookup"><span data-stu-id="dc688-154">Unary call</span></span>

<span data-ttu-id="dc688-155">Unary 呼び出しは、要求メッセージを送信するクライアントで始まります。</span><span class="sxs-lookup"><span data-stu-id="dc688-155">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="dc688-156">サービスが終了すると応答メッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-156">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="dc688-157">*\*.proto* ファイル内の各 Unary サービス メソッドは、メソッドを呼び出すための具象 gRPC クライアント型に関する 2 つの .NET メソッドとなります。非同期のメソッドとブロックするメソッドです。</span><span class="sxs-lookup"><span data-stu-id="dc688-157">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="dc688-158">たとえば `GreeterClient` に関しては、`SayHello` を呼び出す方法は 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="dc688-158">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="dc688-159">`GreeterClient.SayHelloAsync` - `Greeter.SayHello` サービスを非同期に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dc688-159">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="dc688-160">待機可能です。</span><span class="sxs-lookup"><span data-stu-id="dc688-160">Can be awaited.</span></span>
* <span data-ttu-id="dc688-161">`GreeterClient.SayHello` - `Greeter.SayHello` サービスを呼び出して、完了するまでブロックします。</span><span class="sxs-lookup"><span data-stu-id="dc688-161">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="dc688-162">非同期のコードでは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="dc688-162">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="dc688-163">サーバー ストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="dc688-163">Server streaming call</span></span>

<span data-ttu-id="dc688-164">サーバー ストリーミング呼び出しは、要求メッセージを送信するクライアントで始まります。</span><span class="sxs-lookup"><span data-stu-id="dc688-164">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="dc688-165">`ResponseStream.MoveNext()` は、サービスからストリーミングされたメッセージを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="dc688-165">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="dc688-166">`ResponseStream.MoveNext()` が `false` を返すとサーバー ストリーミングの呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="dc688-166">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="dc688-167">C# 8 以降を使用している場合は、`await foreach` 構文を使用してメッセージを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="dc688-167">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="dc688-168">`IAsyncStreamReader<T>.ReadAllAsync()` 拡張メソッドは、応答ストリームからすべてのメッセージを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="dc688-168">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="dc688-169">クライアント ストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="dc688-169">Client streaming call</span></span>

<span data-ttu-id="dc688-170">クライアント ストリーミング呼び出しは、メッセージを送信するクライアント*なしで*始まります。</span><span class="sxs-lookup"><span data-stu-id="dc688-170">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="dc688-171">クライアントでは、`RequestStream.WriteAsync` を使用して、メッセージを送信することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="dc688-171">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="dc688-172">クライアントがメッセージの送信を完了したら、`RequestStream.CompleteAsync()` を呼び出してサービスに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dc688-172">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="dc688-173">サービスが応答メッセージを返すと呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="dc688-173">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="dc688-174">双方向ストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="dc688-174">Bi-directional streaming call</span></span>

<span data-ttu-id="dc688-175">双方向ストリーミング呼び出しは、メッセージを送信するクライアント*なしで*始まります。</span><span class="sxs-lookup"><span data-stu-id="dc688-175">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="dc688-176">クライアントでは、`RequestStream.WriteAsync` を使用して、メッセージを送信することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="dc688-176">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="dc688-177">サービスからストリーミングされたメッセージには、`ResponseStream.MoveNext()` または `ResponseStream.ReadAllAsync()` を使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="dc688-177">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="dc688-178">双方向ストリーミング呼び出しは、`ResponseStream` にメッセージがなくなると完了します。</span><span class="sxs-lookup"><span data-stu-id="dc688-178">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="dc688-179">最適なパフォーマンスを得るため、そしてクライアントとサービスでの不要なエラーを回避するため、双方向のストリーミング呼び出しを正常に完了してみてください。</span><span class="sxs-lookup"><span data-stu-id="dc688-179">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="dc688-180">サーバーが要求ストリームの読み取りを完了し、クライアントが応答ストリームの読み取りを完了すると、双方向呼び出しが正常に完了します。</span><span class="sxs-lookup"><span data-stu-id="dc688-180">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="dc688-181">上記のサンプル呼び出しは、正常に終了する双方向呼び出しの一例です。</span><span class="sxs-lookup"><span data-stu-id="dc688-181">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="dc688-182">この呼び出しでは、クライアントは次を行います。</span><span class="sxs-lookup"><span data-stu-id="dc688-182">In the call, the client:</span></span>

1. <span data-ttu-id="dc688-183">`EchoClient.Echo` を呼び出すことによって、新しい双方向ストリーミング呼び出しを開始します。</span><span class="sxs-lookup"><span data-stu-id="dc688-183">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="dc688-184">`ResponseStream.ReadAllAsync()` を使用してサービスからメッセージを読み取るバックグラウンド タスクを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc688-184">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="dc688-185">`RequestStream.WriteAsync` を使用してサーバーにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="dc688-185">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="dc688-186">`RequestStream.CompleteAsync()` によるメッセージの送信が完了したことをサーバーに通知します。</span><span class="sxs-lookup"><span data-stu-id="dc688-186">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="dc688-187">バックグラウンド タスクがすべての受信メッセージを読み取るまで待機します。</span><span class="sxs-lookup"><span data-stu-id="dc688-187">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="dc688-188">双方向ストリーミング呼び出しの間、クライアントとサービスはいつでも相互にメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="dc688-188">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="dc688-189">双方向呼び出しの操作に最適なクライアント ロジックは、サービス ロジックによってさまざまです。</span><span class="sxs-lookup"><span data-stu-id="dc688-189">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="dc688-190">gRPC トレーラーへのアクセス</span><span class="sxs-lookup"><span data-stu-id="dc688-190">Access gRPC trailers</span></span>

<span data-ttu-id="dc688-191">gRPC の呼び出しによって gRPC トレーラーが返される場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc688-191">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="dc688-192">gRPC トレーラーは、呼び出しに関する名前や値のメタデータを提供するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-192">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="dc688-193">トレーラーにより、同様の機能が HTTP ヘッダーに提供されますが、呼び出しの最後で受信されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-193">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="dc688-194">gRPC トレーラーには、`GetTrailers()` を使用してアクセスできます。これにより、メタデータのコレクションが返されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-194">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="dc688-195">応答が完了した後にトレーラーが返されるため、トレーラーにアクセスする前にすべての応答メッセージを待機する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dc688-195">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="dc688-196">単項呼び出しとクライアント ストリーミング呼び出しでは、`GetTrailers()` を呼び出す前に `ResponseAsync` を待機する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dc688-196">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="dc688-197">サーバーと双方向のストリーミング呼び出しでは、`GetTrailers()` を呼び出す前に応答ストリームの待機を完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dc688-197">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="dc688-198">gRPC トレーラーには、`RpcException` からもアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="dc688-198">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="dc688-199">サービスでは、OK でない gRPC の状態と共にトレーラーが返される場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc688-199">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="dc688-200">このような状況では、トレーラーは、gRPC クライアントによってスローされた例外から取得されます。</span><span class="sxs-lookup"><span data-stu-id="dc688-200">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="dc688-201">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dc688-201">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
