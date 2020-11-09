---
title: gRPC を使用したプロセス間通信
author: jamesnk
description: gRPC を使用してプロセス間通信を行う方法について学習します。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/interprocess
ms.openlocfilehash: d806a340d8540fce8af6ccc6ff68325e4b733922
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059885"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="47092-103">gRPC を使用したプロセス間通信</span><span class="sxs-lookup"><span data-stu-id="47092-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="47092-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="47092-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="47092-105">クライアントとサービス間の gRPC 呼び出しは、通常、TCP ソケットを介して送信されます。</span><span class="sxs-lookup"><span data-stu-id="47092-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="47092-106">TCP は、ネットワークを介して通信するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="47092-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="47092-107">クライアントとサービスが同じマシン上にある場合は、[プロセス間通信 (IPC)](https://wikipedia.org/wiki/Inter-process_communication) の方が TCP より効率的です。</span><span class="sxs-lookup"><span data-stu-id="47092-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="47092-108">このドキュメントでは、IPC シナリオにおいて、カスタム トランスポートで gRPC を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="47092-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="47092-109">サーバー構成</span><span class="sxs-lookup"><span data-stu-id="47092-109">Server configuration</span></span>

<span data-ttu-id="47092-110">カスタム トランスポートは [Kestrel](xref:fundamentals/servers/kestrel) でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="47092-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="47092-111">Kestrel は *Program.cs* で構成されています。</span><span class="sxs-lookup"><span data-stu-id="47092-111">Kestrel is configured in *Program.cs* :</span></span>

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

<span data-ttu-id="47092-112">上記の例の場合:</span><span class="sxs-lookup"><span data-stu-id="47092-112">The preceding example:</span></span>

* <span data-ttu-id="47092-113">`ConfigureKestrel`で Kestrel のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="47092-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="47092-114">指定されたパスを使用して [Unix ドメイン ソケット (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) をリッスンするために、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="47092-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="47092-115">Kestrel には、UDS エンドポイントのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="47092-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="47092-116">UDS は、Linux、macOS、および[最新バージョンの Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="47092-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="47092-117">クライアントの構成</span><span class="sxs-lookup"><span data-stu-id="47092-117">Client configuration</span></span>

<span data-ttu-id="47092-118">`GrpcChannel` では、カスタム トランスポート経由での gRPC 呼び出しがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="47092-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="47092-119">チャネルが作成されると、カスタムの `ConnectCallback` がある `SocketsHttpHandler` で構成できます。</span><span class="sxs-lookup"><span data-stu-id="47092-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="47092-120">コールバックを使用すると、クライアントはカスタム トランスポート経由で接続を確立し、そのトランスポートを介して HTTP 要求を送信できます。</span><span class="sxs-lookup"><span data-stu-id="47092-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47092-121">`SocketsHttpHandler.ConnectCallback` は、.NET 5 リリース候補 2 の新しい API です。</span><span class="sxs-lookup"><span data-stu-id="47092-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="47092-122">Unix ドメイン ソケットの接続ファクトリの例:</span><span class="sxs-lookup"><span data-stu-id="47092-122">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

<span data-ttu-id="47092-123">カスタム接続ファクトリを使用してチャネルを作成する場合:</span><span class="sxs-lookup"><span data-stu-id="47092-123">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="47092-124">上記のコードを使用して作成されたチャネルの場合、Unix ドメイン ソケット経由で gRPC 呼び出しが送信されます。</span><span class="sxs-lookup"><span data-stu-id="47092-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="47092-125">他の IPC テクノロジのサポートは、Kestrel と `SocketsHttpHandler` の機能拡張を使用して実装できます。</span><span class="sxs-lookup"><span data-stu-id="47092-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
