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
# <a name="inter-process-communication-with-grpc"></a>gRPC を使用したプロセス間通信

作成者: [James Newton-King](https://twitter.com/jamesnk)

クライアントとサービス間の gRPC 呼び出しは、通常、TCP ソケットを介して送信されます。 TCP はネットワーク経由の通信に適していますが、クライアントとサービスが同じコンピューター上にある場合は、[プロセス間通信 (IPC)](https://wikipedia.org/wiki/Inter-process_communication) の方が効率的です。 このドキュメントでは、IPC シナリオにおいて、カスタム トランスポートで gRPC を使用する方法について説明します。

## <a name="server-configuration"></a>サーバー構成

カスタム トランスポートは Kestrel でサポートされています。 Kestrel は *Program.cs* で構成されています。

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
* 指定されたパスを使用して [Unix ドメイン ソケット (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) をリッスンするために、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を呼び出します。

Kestrel には、UDS エンドポイントのサポートが組み込まれています。 UDS は、Linux、macOS、および[最新バージョンの Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) でサポートされています。

## <a name="client-configuration"></a>クライアントの構成

`GrpcChannel` では、カスタム トランスポート経由での gRPC 呼び出しがサポートされます。 チャネルが作成されると、カスタムの `ConnectionFactory` がある `SocketsHttpHandler` で構成できます。 ファクトリを使用すると、クライアントはカスタム トランスポート経由で接続を確立し、そのトランスポートを介して HTTP 要求を送信できます。

> [!IMPORTANT]
> `ConnectionFactory` は、.NET 5 リリース候補 1 の新しい API です。

Unix ドメイン ソケットの接続ファクトリの例:

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

カスタム接続ファクトリを使用してチャネルを作成する場合:

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

上記のコードを使用して作成されたチャネルの場合、Unix ドメイン ソケット経由で gRPC 呼び出しが送信されます。
