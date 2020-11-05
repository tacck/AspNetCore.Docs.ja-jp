---
title: gRPC を使用したプロセス間通信
author: jamesnk
description: gRPC を使用してプロセス間通信を行う方法について学習します。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
uid: grpc/interprocess
ms.openlocfilehash: d806a340d8540fce8af6ccc6ff68325e4b733922
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059885"
---
# <a name="inter-process-communication-with-grpc"></a>gRPC を使用したプロセス間通信

作成者: [James Newton-King](https://twitter.com/jamesnk)

クライアントとサービス間の gRPC 呼び出しは、通常、TCP ソケットを介して送信されます。 TCP は、ネットワークを介して通信するように設計されています。 クライアントとサービスが同じマシン上にある場合は、[プロセス間通信 (IPC)](https://wikipedia.org/wiki/Inter-process_communication) の方が TCP より効率的です。 このドキュメントでは、IPC シナリオにおいて、カスタム トランスポートで gRPC を使用する方法について説明します。

## <a name="server-configuration"></a>サーバー構成

カスタム トランスポートは [Kestrel](xref:fundamentals/servers/kestrel) でサポートされています。 Kestrel は *Program.cs* で構成されています。

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

上記の例の場合:

* `ConfigureKestrel`で Kestrel のエンドポイントを構成します。
* 指定されたパスを使用して [Unix ドメイン ソケット (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) をリッスンするために、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を呼び出します。

Kestrel には、UDS エンドポイントのサポートが組み込まれています。 UDS は、Linux、macOS、および[最新バージョンの Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) でサポートされています。

## <a name="client-configuration"></a>クライアントの構成

`GrpcChannel` では、カスタム トランスポート経由での gRPC 呼び出しがサポートされます。 チャネルが作成されると、カスタムの `ConnectCallback` がある `SocketsHttpHandler` で構成できます。 コールバックを使用すると、クライアントはカスタム トランスポート経由で接続を確立し、そのトランスポートを介して HTTP 要求を送信できます。

> [!IMPORTANT]
> `SocketsHttpHandler.ConnectCallback` は、.NET 5 リリース候補 2 の新しい API です。

Unix ドメイン ソケットの接続ファクトリの例:

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

カスタム接続ファクトリを使用してチャネルを作成する場合:

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

上記のコードを使用して作成されたチャネルの場合、Unix ドメイン ソケット経由で gRPC 呼び出しが送信されます。 他の IPC テクノロジのサポートは、Kestrel と `SocketsHttpHandler` の機能拡張を使用して実装できます。
