---
title: ASP.NET Core SignalR .Net クライアント
author: bradygaster
description: ASP.NET Core .Net クライアントに関する情報 SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 54e86479b9f9f0acc861769f9ab78958f79acfd3
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400142"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="59395-103">ASP.NET Core SignalR .Net クライアント</span><span class="sxs-lookup"><span data-stu-id="59395-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="59395-104">ASP.NET Core SignalR .net クライアントライブラリを使用すると、 SignalR .net アプリからハブと通信できます。</span><span class="sxs-lookup"><span data-stu-id="59395-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="59395-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="59395-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="59395-106">この記事のコードサンプルは、ASP.NET Core .Net クライアントを使用する WPF アプリです SignalR 。</span><span class="sxs-lookup"><span data-stu-id="59395-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="59395-107">SignalR.Net クライアントパッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="59395-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="59395-108">[AspNetCore. SignalR .クライアント](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)パッケージは、.net クライアントがハブに接続するために必要です SignalR 。</span><span class="sxs-lookup"><span data-stu-id="59395-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59395-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59395-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59395-110">クライアントライブラリをインストールするには、**パッケージマネージャーコンソール**ウィンドウで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="59395-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="59395-111">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="59395-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="59395-112">クライアントライブラリをインストールするには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="59395-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="59395-113">ハブへの接続</span><span class="sxs-lookup"><span data-stu-id="59395-113">Connect to a hub</span></span>

<span data-ttu-id="59395-114">接続を確立するには、を作成し、を `HubConnectionBuilder` 呼び出し `Build` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="59395-115">接続の構築中に、ハブの URL、プロトコル、トランスポートの種類、ログレベル、ヘッダー、およびその他のオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="59395-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="59395-116">任意のメソッドをに挿入して、必要なオプションを構成 `HubConnectionBuilder` `Build` します。</span><span class="sxs-lookup"><span data-stu-id="59395-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="59395-117">との接続を開始 `StartAsync` します。</span><span class="sxs-lookup"><span data-stu-id="59395-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="59395-118">失われた接続の処理</span><span class="sxs-lookup"><span data-stu-id="59395-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="59395-119">自動的に再接続する</span><span class="sxs-lookup"><span data-stu-id="59395-119">Automatically reconnect</span></span>

<span data-ttu-id="59395-120">は、の <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> メソッドを使用して自動的に再接続するように構成でき `WithAutomaticReconnect` <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="59395-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="59395-121">既定では、自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="59395-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="59395-122">パラメーターを指定しない場合、は、 `WithAutomaticReconnect()` 再接続を試行する前に、それぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。</span><span class="sxs-lookup"><span data-stu-id="59395-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="59395-123">再接続の試行を開始する前に、は `HubConnection` 状態に遷移し、 `HubConnectionState.Reconnecting` イベントを発生させ `Reconnecting` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="59395-124">これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="59395-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="59395-125">非対話型アプリでは、メッセージのキューまたは削除を開始できます。</span><span class="sxs-lookup"><span data-stu-id="59395-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="59395-126">最初の4回の試行でクライアントが正常に再接続した場合、は `HubConnection` 状態に戻り、 `Connected` イベントを発生させ `Reconnected` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="59395-127">これにより、接続が再確立されたことをユーザーに通知し、キューに置かれたすべてのメッセージをデキューすることができます。</span><span class="sxs-lookup"><span data-stu-id="59395-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="59395-128">接続はサーバーにまったく新しいものであるため、 `ConnectionId` イベントハンドラーに新しいが提供され `Reconnected` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="59395-129">が `Reconnected` `connectionId` `HubConnection` [ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)するように構成されている場合、イベントハンドラーのパラメーターは null になります。</span><span class="sxs-lookup"><span data-stu-id="59395-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="59395-130">`WithAutomaticReconnect()`最初の開始エラーを再試行するようにを構成しません `HubConnection` 。そのため、開始エラーは手動で処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59395-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="59395-131">最初の4回の試行でクライアントが正常に再接続されない場合、は `HubConnection` 状態に遷移し、 `Disconnected` イベントを発生させ <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> ます。</span><span class="sxs-lookup"><span data-stu-id="59395-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="59395-132">これにより、接続を手動で再起動したり、接続が完全に失われたことをユーザーに通知したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="59395-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="59395-133">再接続のタイミングを切断または変更する前に、カスタムの再接続試行回数を構成するために、では、 `WithAutomaticReconnect` 各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="59395-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="59395-134">前の例では、 `HubConnection` 接続が失われた直後に再接続を開始するようにを構成しています。</span><span class="sxs-lookup"><span data-stu-id="59395-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="59395-135">これは、既定の構成にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="59395-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="59395-136">最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。</span><span class="sxs-lookup"><span data-stu-id="59395-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="59395-137">2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。</span><span class="sxs-lookup"><span data-stu-id="59395-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="59395-138">その後、3回目の再接続の試行が失敗した後に停止することで、カスタム動作が既定の動作から再び逸脱します。</span><span class="sxs-lookup"><span data-stu-id="59395-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="59395-139">既定の構成では、もう1回は30秒後に再接続が試行されます。</span><span class="sxs-lookup"><span data-stu-id="59395-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="59395-140">タイミングと自動再接続試行回数をさらに細かく制御する場合は、 `WithAutomaticReconnect` インターフェイスを実装するオブジェクトを受け取り `IRetryPolicy` ます。このインターフェイスには、という名前の1つのメソッドがあり `NextRetryDelay` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="59395-141">`NextRetryDelay`型の1つの引数を受け取り `RetryContext` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="59395-142">には、、、およびの `RetryContext` 3 つのプロパティがあり `PreviousRetryCount` `ElapsedTime` `RetryReason` `long` `TimeSpan` `Exception` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="59395-143">最初の再接続を試行する前に、 `PreviousRetryCount` と `ElapsedTime` の両方がゼロになり、は接続が失われる `RetryReason` 原因となった例外になります。</span><span class="sxs-lookup"><span data-stu-id="59395-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="59395-144">再試行が再試行されるたびに、が1ずつインクリメントされ、これ `PreviousRetryCount` `ElapsedTime` までに再接続に費やされた時間が反映されます。また、は、 `RetryReason` 最後の再接続の試行が失敗した原因となった例外になります。</span><span class="sxs-lookup"><span data-stu-id="59395-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="59395-145">`NextRetryDelay`次の再接続が試行される前に待機する時間を表す TimeSpan を返すか、またはの再接続を停止する必要があり `null` `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="59395-146">または、[手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="59395-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="59395-147">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="59395-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="59395-148">3.0 より前では、の .NET クライアントは SignalR 自動的に再接続しません。</span><span class="sxs-lookup"><span data-stu-id="59395-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="59395-149">クライアントを手動で再接続するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59395-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="59395-150"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>失われた接続に応答するには、イベントを使用します。</span><span class="sxs-lookup"><span data-stu-id="59395-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="59395-151">たとえば、再接続を自動化することができます。</span><span class="sxs-lookup"><span data-stu-id="59395-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="59395-152">イベントには、を `Closed` 返すデリゲートが必要です `Task` 。これにより、を使用せずに非同期コードを実行でき `async void` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="59395-153">同期的に実行されるイベントハンドラーでデリゲートシグネチャを満たすには `Closed` 、次を返し `Task.CompletedTask` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="59395-154">非同期サポートの主な理由は、接続を再起動できるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="59395-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="59395-155">接続の開始は、非同期アクションです。</span><span class="sxs-lookup"><span data-stu-id="59395-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="59395-156">接続を `Closed` 再起動するハンドラーで、次の例に示すように、サーバーの過負荷を防ぐために、ランダムな遅延を待機することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="59395-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="59395-157">クライアントからのハブメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="59395-157">Call hub methods from client</span></span>

<span data-ttu-id="59395-158">`InvokeAsync`ハブでメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59395-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="59395-159">ハブメソッドの名前と、ハブメソッドで定義されているすべての引数をに渡し `InvokeAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> SignalR<span data-ttu-id="59395-160">は非同期であるため `async` 、 `await` 呼び出しを行うときにとを使用します。</span><span class="sxs-lookup"><span data-stu-id="59395-160"> is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="59395-161">この `InvokeAsync` メソッドは、 `Task` サーバーメソッドから制御が戻ったときに完了するを返します。</span><span class="sxs-lookup"><span data-stu-id="59395-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="59395-162">戻り値 (存在する場合) は、の結果として提供され `Task` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="59395-163">サーバー上のメソッドによってスローされた例外が発生すると、エラーが発生 `Task` します。</span><span class="sxs-lookup"><span data-stu-id="59395-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="59395-164">構文を使用して `await` 、サーバーメソッドが完了するのを待機し、構文を使用して `try...catch` エラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="59395-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="59395-165">メソッドは、 `SendAsync` `Task` メッセージがサーバーに送信されたときに完了するを返します。</span><span class="sxs-lookup"><span data-stu-id="59395-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="59395-166">`Task`サーバーメソッドが完了するまで待機しないため、戻り値は提供されません。</span><span class="sxs-lookup"><span data-stu-id="59395-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="59395-167">メッセージの送信中にクライアントでスローされた例外は、エラーを生成し `Task` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="59395-168">および構文を使用し `await` て、 `try...catch` 送信エラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="59395-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="59395-169">SignalR*サーバーレスモード*で Azure サービスを使用している場合は、クライアントからハブメソッドを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="59395-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="59395-170">詳細については、 [ SignalR サービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59395-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="59395-171">ハブからクライアントメソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="59395-171">Call client methods from hub</span></span>

<span data-ttu-id="59395-172">ハブが `connection.On` ビルド後、接続を開始する前にを使用して呼び出すメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="59395-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="59395-173">の前のコードは、 `connection.On` サーバー側のコードがメソッドを使用して呼び出したときに実行され `SendAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="59395-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="59395-174">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="59395-174">Error handling and logging</span></span>

<span data-ttu-id="59395-175">Try-catch ステートメントを使用してエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="59395-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="59395-176">オブジェクトを調べて、 `Exception` エラーが発生した後に実行する適切なアクションを決定します。</span><span class="sxs-lookup"><span data-stu-id="59395-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="59395-177">その他の資料</span><span class="sxs-lookup"><span data-stu-id="59395-177">Additional resources</span></span>

* [<span data-ttu-id="59395-178">ハブ</span><span class="sxs-lookup"><span data-stu-id="59395-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="59395-179">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="59395-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="59395-180">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="59395-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* <span data-ttu-id="59395-181">[Azure SignalR サービスのサーバーレスドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="59395-181">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
