---
title: ASP.NET Core SignalRスケールアウトのための Redis バックプレーン
author: bradygaster
description: Redis バックプレーンを設定して、ASP.NET Core SignalRアプリのスケールアウトを有効にする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 6068890f4089a13add05bf8cf8009367e343adce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775350"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="7caa4-103">ASP.NET Core SignalRスケールアウトのために Redis バックプレーンを設定する</span><span class="sxs-lookup"><span data-stu-id="7caa4-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="7caa4-104">[Andrew](https://twitter.com/anurse)、 [Brady](https://twitter.com/bradygaster)、および[Tom Dykstra](https://github.com/tdykstra)によって、</span><span class="sxs-lookup"><span data-stu-id="7caa4-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="7caa4-105">この記事でSignalRは、ASP.NET Core SignalRアプリをスケールアウトするために使用する[Redis](https://redis.io/)サーバーの設定の具体的な側面について説明します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="7caa4-106">Redis バックプレーンを設定する</span><span class="sxs-lookup"><span data-stu-id="7caa4-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="7caa4-107">Redis サーバーをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="7caa4-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="7caa4-108">運用環境で使用する場合、Redis バックプレーンは、 SignalRアプリと同じデータセンターで実行されている場合にのみお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7caa4-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="7caa4-109">そうしないと、ネットワーク待機時間がパフォーマンスを低下します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="7caa4-110">SignalRアプリが azure クラウドで実行されている場合は、 SignalR Redis バックプレーンの代わりに azure サービスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7caa4-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="7caa4-111">開発環境とテスト環境では、Azure Redis Cache サービスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="7caa4-112">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7caa4-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="7caa4-113">Redis のドキュメント</span><span class="sxs-lookup"><span data-stu-id="7caa4-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="7caa4-114">Azure Redis Cache のドキュメント</span><span class="sxs-lookup"><span data-stu-id="7caa4-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="7caa4-115">SignalRアプリで、 `Microsoft.AspNetCore.SignalR.Redis` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7caa4-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="7caa4-116">`Startup.ConfigureServices`メソッドで、の後`AddRedis` `AddSignalR`にを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="7caa4-117">必要に応じてオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="7caa4-118">ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="7caa4-119">で`ConfigurationOptions`指定されたオプションは、接続文字列で設定されたオプションよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="7caa4-120">次の例は、 `ConfigurationOptions`オブジェクトのオプションを設定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7caa4-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="7caa4-121">この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="7caa4-122">上記のコードでは`options.Configuration` 、は、接続文字列で指定された値で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="7caa4-123">SignalRアプリで、次のいずれかの NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7caa4-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="7caa4-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-StackExchange に依存します。 Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="7caa4-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="7caa4-125">これは ASP.NET Core 2.2 以降で推奨されるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="7caa4-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="7caa4-126">`Microsoft.AspNetCore.SignalR.Redis`-StackExchange に依存します。 Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="7caa4-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="7caa4-127">このパッケージは ASP.NET Core 3.0 以降には含まれていません。</span><span class="sxs-lookup"><span data-stu-id="7caa4-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="7caa4-128">`Startup.ConfigureServices`メソッドで、次の<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>ように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="7caa4-129">を使用`Microsoft.AspNetCore.SignalR.Redis`する場合<xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>は、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="7caa4-130">必要に応じてオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="7caa4-131">ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="7caa4-132">で`ConfigurationOptions`指定されたオプションは、接続文字列で設定されたオプションよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="7caa4-133">次の例は、 `ConfigurationOptions`オブジェクトのオプションを設定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7caa4-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="7caa4-134">この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="7caa4-135">を使用`Microsoft.AspNetCore.SignalR.Redis`する場合<xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>は、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="7caa4-136">上記のコードでは`options.Configuration` 、は、接続文字列で指定された値で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="7caa4-137">Redis オプションの詳細については、 [Stackexchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7caa4-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="7caa4-138">SignalRアプリで、次の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7caa4-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="7caa4-139">`Startup.ConfigureServices`メソッドで、次の<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>ように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="7caa4-140">必要に応じてオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="7caa4-141">ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="7caa4-142">で`ConfigurationOptions`指定されたオプションは、接続文字列で設定されたオプションよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="7caa4-143">次の例は、 `ConfigurationOptions`オブジェクトのオプションを設定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7caa4-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="7caa4-144">この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="7caa4-145">上記のコードでは`options.Configuration` 、は、接続文字列で指定された値で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="7caa4-146">Redis オプションの詳細については、 [Stackexchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7caa4-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="7caa4-147">複数SignalRのアプリに対して1つの Redis サーバーを使用している場合はSignalR 、アプリごとに異なるチャネルプレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="7caa4-148">チャネルプレフィックスを設定するとSignalR 、別のチャネルプレフィックスを使用する他のアプリとは分離されます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="7caa4-149">異なるプレフィックスを割り当てない場合、1つのアプリからすべてのクライアントに送信されるメッセージは、Redis サーバーをバックプレーンとして使用するすべてのアプリのすべてのクライアントに送られます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="7caa4-150">固定セッション用にサーバーファームの負荷分散ソフトウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="7caa4-151">その方法に関するドキュメントの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="7caa4-152">IIS</span><span class="sxs-lookup"><span data-stu-id="7caa4-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="7caa4-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="7caa4-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="7caa4-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="7caa4-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="7caa4-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="7caa4-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="7caa4-156">Redis サーバーのエラー</span><span class="sxs-lookup"><span data-stu-id="7caa4-156">Redis server errors</span></span>

<span data-ttu-id="7caa4-157">Redis サーバーがダウンすると、 SignalRはメッセージが配信されないことを示す例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="7caa4-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="7caa4-158">一般的な例外メッセージを次に示します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-158">Some typical exception messages:</span></span>

* <span data-ttu-id="7caa4-159">*メッセージの書き込みに失敗しました*</span><span class="sxs-lookup"><span data-stu-id="7caa4-159">*Failed writing message*</span></span>
* <span data-ttu-id="7caa4-160">*ハブメソッド ' MethodName ' を呼び出すことができませんでした*</span><span class="sxs-lookup"><span data-stu-id="7caa4-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="7caa4-161">*Redis への接続に失敗しました*</span><span class="sxs-lookup"><span data-stu-id="7caa4-161">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="7caa4-162">は、サーバーがバックアップされたときにメッセージを送信するようにメッセージをバッファリングしません。</span><span class="sxs-lookup"><span data-stu-id="7caa4-162"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="7caa4-163">Redis サーバーが停止している間に送信されたメッセージはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="7caa4-163">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="7caa4-164">Redis サーバーが再び使用可能になったときに、自動的に再接続します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-164"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="7caa4-165">接続エラーのカスタム動作</span><span class="sxs-lookup"><span data-stu-id="7caa4-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="7caa4-166">Redis 接続エラーイベントの処理方法を示す例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="7caa4-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="7caa4-167">Redis クラスタリング</span><span class="sxs-lookup"><span data-stu-id="7caa4-167">Redis Clustering</span></span>

<span data-ttu-id="7caa4-168">[Redis クラスタリング](https://redis.io/topics/cluster-spec)は、複数の redis サーバーを使用して高可用性を実現するための方法です。</span><span class="sxs-lookup"><span data-stu-id="7caa4-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="7caa4-169">クラスタリングは正式にはサポートされていませんが、動作する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7caa4-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7caa4-170">次のステップ</span><span class="sxs-lookup"><span data-stu-id="7caa4-170">Next steps</span></span>

<span data-ttu-id="7caa4-171">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7caa4-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="7caa4-172">Redis のドキュメント</span><span class="sxs-lookup"><span data-stu-id="7caa4-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="7caa4-173">StackExchange Redis のドキュメント</span><span class="sxs-lookup"><span data-stu-id="7caa4-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="7caa4-174">Azure Redis Cache のドキュメント</span><span class="sxs-lookup"><span data-stu-id="7caa4-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
