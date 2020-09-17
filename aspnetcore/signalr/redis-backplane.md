---
title: ASP.NET Core スケールアウトのための Redis バックプレーン SignalR
author: bradygaster
description: Redis バックプレーンを設定して、ASP.NET Core アプリのスケールアウトを有効にする方法について説明し SignalR ます。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/redis-backplane
ms.openlocfilehash: a57176409c5f9bcc620db7e070f6616951eb9a54
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722775"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a>ASP.NET Core スケールアウトのために Redis バックプレーンを設定する SignalR

[Andrew](https://twitter.com/anurse)、 [Brady](https://twitter.com/bradygaster)、および[Tom Dykstra](https://github.com/tdykstra)によって、

この記事では、 SignalR ASP.NET Core アプリをスケールアウトするために使用する [Redis](https://redis.io/) サーバーの設定の具体的な側面について説明 SignalR します。

## <a name="set-up-a-redis-backplane"></a>Redis バックプレーンを設定する

* Redis サーバーをデプロイします。

  > [!IMPORTANT] 
  > 運用環境で使用する場合、Redis バックプレーンは、アプリと同じデータセンターで実行されている場合にのみお勧めし SignalR ます。 そうしないと、ネットワーク待機時間がパフォーマンスを低下します。 SignalRアプリが azure クラウドで実行されている場合は、 SignalR Redis バックプレーンの代わりに azure サービスを使用することをお勧めします。 開発環境とテスト環境では、Azure Redis Cache サービスを使用できます。

  詳細については、次のリソースを参照してください。

  * <xref:signalr/scale>
  * [Redis のドキュメント](https://redis.io/)
  * [Azure Redis Cache のドキュメント](/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* アプリで、 SignalR NuGet パッケージをインストールし `Microsoft.AspNetCore.SignalR.Redis` ます。
* メソッドで `Startup.ConfigureServices` 、の後にを呼び出し `AddRedis` `AddSignalR` ます。

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* 必要に応じてオプションを構成します。
 
  ほとんどのオプションは、接続文字列または [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) オブジェクトで設定できます。 で指定されたオプション `ConfigurationOptions` は、接続文字列で設定されたオプションよりも優先されます。

  次の例は、オブジェクトのオプションを設定する方法を示して `ConfigurationOptions` います。 この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  上記のコードで `options.Configuration` は、は、接続文字列で指定された値で初期化されます。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* アプリで、 SignalR 次のいずれかの NuGet パッケージをインストールします。

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` -StackExchange に依存します。 Redis 2. X.X. これは ASP.NET Core 2.2 以降で推奨されるパッケージです。
  * `Microsoft.AspNetCore.SignalR.Redis` -StackExchange に依存します。 Redis 1. X.X. このパッケージは ASP.NET Core 3.0 以降には含まれていません。

* メソッドで `Startup.ConfigureServices` 、次の <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> ように呼び出します。

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 を使用する場合 `Microsoft.AspNetCore.SignalR.Redis` は、を呼び出し <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> ます。

* 必要に応じてオプションを構成します。
 
  ほとんどのオプションは、接続文字列または [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) オブジェクトで設定できます。 で指定されたオプション `ConfigurationOptions` は、接続文字列で設定されたオプションよりも優先されます。

  次の例は、オブジェクトのオプションを設定する方法を示して `ConfigurationOptions` います。 この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 を使用する場合 `Microsoft.AspNetCore.SignalR.Redis` は、を呼び出し <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> ます。

  上記のコードで `options.Configuration` は、は、接続文字列で指定された値で初期化されます。

  Redis オプションの詳細については、 [Stackexchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)を参照してください。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* アプリで SignalR 、次の NuGet パッケージをインストールします。

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* メソッドで `Startup.ConfigureServices` 、次の <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> ように呼び出します。

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* 必要に応じてオプションを構成します。
 
  ほとんどのオプションは、接続文字列または [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) オブジェクトで設定できます。 で指定されたオプション `ConfigurationOptions` は、接続文字列で設定されたオプションよりも優先されます。

  次の例は、オブジェクトのオプションを設定する方法を示して `ConfigurationOptions` います。 この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  上記のコードで `options.Configuration` は、は、接続文字列で指定された値で初期化されます。

  Redis オプションの詳細については、 [Stackexchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)を参照してください。

::: moniker-end

* 複数のアプリに対して1つの Redis サーバーを使用している場合は SignalR 、アプリごとに異なるチャネルプレフィックスを使用し SignalR ます。

  チャネルプレフィックスを設定すると、 SignalR 別のチャネルプレフィックスを使用する他のアプリとは分離されます。 異なるプレフィックスを割り当てない場合、1つのアプリからすべてのクライアントに送信されるメッセージは、Redis サーバーをバックプレーンとして使用するすべてのアプリのすべてのクライアントに送られます。

* 固定セッション用にサーバーファームの負荷分散ソフトウェアを構成します。 その方法に関するドキュメントの例を次に示します。

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Redis サーバーのエラー

Redis サーバーがダウンすると、は SignalR メッセージが配信されないことを示す例外をスローします。 一般的な例外メッセージを次に示します。

* *メッセージの書き込みに失敗しました*
* *ハブメソッド ' MethodName ' を呼び出すことができませんでした*
* *Redis への接続に失敗しました*

SignalR は、サーバーがバックアップされたときにメッセージを送信するようにメッセージをバッファリングしません。 Redis サーバーが停止している間に送信されたメッセージはすべて失われます。

SignalR Redis サーバーが再び使用可能になったときに、自動的に再接続します。

### <a name="custom-behavior-for-connection-failures"></a>接続エラーのカスタム動作

Redis 接続エラーイベントの処理方法を示す例を次に示します。

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a>Redis クラスタリング

[Redis クラスタリング](https://redis.io/topics/cluster-spec) は、複数の redis サーバーを使用して高可用性を実現するための方法です。 クラスタリングは正式にはサポートされていませんが、動作する可能性があります。

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* <xref:signalr/scale>
* [Redis のドキュメント](https://redis.io/documentation)
* [StackExchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/)
* [Azure Redis Cache のドキュメント](/azure/redis-cache/)