---
title: gRPC を使用したプロセス間通信
author: jamesnk
description: gRPC を使用してプロセス間通信を行う方法について学習します。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945522"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="a97c5-103">gRPC を使用したプロセス間通信</span><span class="sxs-lookup"><span data-stu-id="a97c5-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="a97c5-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a97c5-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="a97c5-105">クライアントとサービス間の gRPC 呼び出しは、通常、TCP ソケットを介して送信されます。</span><span class="sxs-lookup"><span data-stu-id="a97c5-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="a97c5-106">TCP はネットワーク経由の通信に適していますが、クライアントとサービスが同じコンピューター上にある場合は、[プロセス間通信 (IPC)](https://wikipedia.org/wiki/Inter-process_communication) の方が効率的です。</span><span class="sxs-lookup"><span data-stu-id="a97c5-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="a97c5-107">このドキュメントでは、IPC シナリオにおいて、カスタム トランスポートで gRPC を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a97c5-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="a97c5-108">サーバー構成</span><span class="sxs-lookup"><span data-stu-id="a97c5-108">Server configuration</span></span>

<span data-ttu-id="a97c5-109">カスタム トランスポートは Kestrel でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a97c5-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="a97c5-110">Kestrel は *Program.cs* で構成されています。</span><span class="sxs-lookup"><span data-stu-id="a97c5-110">Kestrel is configured in *Program.cs*:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

<span data-ttu-id="a97c5-111">上記の例の場合:</span><span class="sxs-lookup"><span data-stu-id="a97c5-111">The preceding example:</span></span>

* <span data-ttu-id="a97c5-112">`ConfigureKestrel`で Kestrel のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="a97c5-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="a97c5-113">指定されたパスを使用して [Unix ドメイン ソケット (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) をリッスンするために、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a97c5-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="a97c5-114">Kestrel には、UDS エンドポイントのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="a97c5-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="a97c5-115">UDS は、Linux、macOS、および[最新バージョンの Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a97c5-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="a97c5-116">クライアントの構成</span><span class="sxs-lookup"><span data-stu-id="a97c5-116">Client configuration</span></span>

<span data-ttu-id="a97c5-117">`GrpcChannel` では、カスタム トランスポート経由での gRPC 呼び出しがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="a97c5-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="a97c5-118">チャネルが作成されると、カスタムの `ConnectionFactory` がある `SocketsHttpHandler` で構成できます。</span><span class="sxs-lookup"><span data-stu-id="a97c5-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="a97c5-119">ファクトリを使用すると、クライアントはカスタム トランスポート経由で接続を確立し、そのトランスポートを介して HTTP 要求を送信できます。</span><span class="sxs-lookup"><span data-stu-id="a97c5-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a97c5-120">`ConnectionFactory` は、.NET 5 リリース候補 1 の新しい API です。</span><span class="sxs-lookup"><span data-stu-id="a97c5-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="a97c5-121">Unix ドメイン ソケットの接続ファクトリの例:</span><span class="sxs-lookup"><span data-stu-id="a97c5-121">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

<span data-ttu-id="a97c5-122">カスタム接続ファクトリを使用してチャネルを作成する場合:</span><span class="sxs-lookup"><span data-stu-id="a97c5-122">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="a97c5-123">上記のコードを使用して作成されたチャネルの場合、Unix ドメイン ソケット経由で gRPC 呼び出しが送信されます。</span><span class="sxs-lookup"><span data-stu-id="a97c5-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
