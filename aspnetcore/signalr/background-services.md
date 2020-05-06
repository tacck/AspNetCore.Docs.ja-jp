---
title: バックグラウンドサービスSignalRでのホスト ASP.NET Core
author: bradygaster
description: .NET Core BackgroundService クラスからSignalRクライアントにメッセージを送信する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777294"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="bb010-103">バックグラウンドサービスSignalRでのホスト ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bb010-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="bb010-104">[Brady](https://twitter.com/bradygaster)による</span><span class="sxs-lookup"><span data-stu-id="bb010-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="bb010-105">この記事では、次のガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="bb010-105">This article provides guidance for:</span></span>

* <span data-ttu-id="bb010-106">ASP.NET Core SignalRでホストされたバックグラウンドワーカープロセスを使用してハブをホストする。</span><span class="sxs-lookup"><span data-stu-id="bb010-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="bb010-107">.NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)内から接続されたクライアントにメッセージを送信する。</span><span class="sxs-lookup"><span data-stu-id="bb010-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="bb010-108">[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="bb010-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="bb010-109">スタートアップSignalRで有効にする</span><span class="sxs-lookup"><span data-stu-id="bb010-109">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb010-110">バックグラウンドワーカー SignalRプロセスのコンテキストで ASP.NET Core ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。</span><span class="sxs-lookup"><span data-stu-id="bb010-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="bb010-111">`Startup.ConfigureServices`メソッドでを呼び出す`services.AddSignalR`と、をサポートSignalRするために必要なサービスが ASP.NET Core 依存関係挿入 (DI) 層に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bb010-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="bb010-112">で`Startup.Configure`は、 `MapHub`メソッドが`UseEndpoints`コールバックで呼び出され、ASP.NET Core 要求パイプラインのハブエンドポイントに接続します。</span><span class="sxs-lookup"><span data-stu-id="bb010-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="bb010-113">バックグラウンドワーカー SignalRプロセスのコンテキストで ASP.NET Core ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。</span><span class="sxs-lookup"><span data-stu-id="bb010-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="bb010-114">`Startup.ConfigureServices`メソッドでを呼び出す`services.AddSignalR`と、をサポートSignalRするために必要なサービスが ASP.NET Core 依存関係挿入 (DI) 層に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bb010-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="bb010-115">で`Startup.Configure`は、 `UseSignalR`メソッドを呼び出して、ASP.NET Core 要求パイプラインのハブエンドポイントを接続します。</span><span class="sxs-lookup"><span data-stu-id="bb010-115">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="bb010-116">前の例では、 `ClockHub`クラスは、 `Hub<T>`厳密に型指定されたハブを作成するクラスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="bb010-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="bb010-117">は`ClockHub` 、エンドポイント`/hubs/clock`での`Startup`要求に応答するようにクラスで構成されています。</span><span class="sxs-lookup"><span data-stu-id="bb010-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="bb010-118">厳密に型指定されたハブの詳細については、「 [ SignalRのハブを使用](xref:signalr/hubs#strongly-typed-hubs)した ASP.NET Core」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bb010-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="bb010-119">この機能は[\<ハブ t>](xref:Microsoft.AspNetCore.SignalR.Hub`1)クラスに限定されません。</span><span class="sxs-lookup"><span data-stu-id="bb010-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="bb010-120">また、 [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)など、[ハブ](xref:Microsoft.AspNetCore.SignalR.Hub)から継承されるすべてのクラスも機能します。</span><span class="sxs-lookup"><span data-stu-id="bb010-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="bb010-121">厳密に型指定`ClockHub`されたによっ`IClock`て使用されるインターフェイスは、インターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="bb010-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="bb010-122">バックグラウンドサービスSignalRからハブを呼び出す</span><span class="sxs-lookup"><span data-stu-id="bb010-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="bb010-123">起動時に、 `Worker`クラス`BackgroundService`はを使用`AddHostedService`して有効になります。</span><span class="sxs-lookup"><span data-stu-id="bb010-123">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="bb010-124">はSignalR `Startup`フェーズ中にも有効になるため、各ハブは ASP.NET Core の HTTP 要求パイプライン内の個々のエンドポイントにアタッチされます。各ハブ`IHubContext<T>`は、サーバー上のによって表されます。</span><span class="sxs-lookup"><span data-stu-id="bb010-124">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="bb010-125">ASP.NET Core の DI 機能を使用して、 `BackgroundService`クラス、MVC コントローラークラス、 Razorページモデルなどのホスト層によってインスタンス化された他のクラスは、構築中に`IHubContext<ClockHub, IClock>`のインスタンスを受け入れることによって、サーバー側ハブへの参照を取得できます。</span><span class="sxs-lookup"><span data-stu-id="bb010-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="bb010-126">バックグラウンドサービス`ExecuteAsync`でメソッドが繰り返し呼び出されると、サーバーの現在の日付と時刻が、 `ClockHub`を使用して接続されたクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="bb010-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="bb010-127">バックグラウンドサービスSignalRを使用したイベントへの対応</span><span class="sxs-lookup"><span data-stu-id="bb010-127">React to SignalR events with background services</span></span>

<span data-ttu-id="bb010-128">用SignalRの JavaScript クライアントを使用するシングルページアプリの場合と同様に、.net デスクトップアプリでは<xref:signalr/dotnet-client>を使用`BackgroundService`し`IHostedService`てを使用できますが、またSignalRはの実装を使用してハブに接続し、イベントに応答することもできます。</span><span class="sxs-lookup"><span data-stu-id="bb010-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="bb010-129">クラス`ClockHubClient`は、インターフェイスと`IClock` `IHostedService`インターフェイスの両方を実装します。</span><span class="sxs-lookup"><span data-stu-id="bb010-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="bb010-130">このようにして、の`Startup`実行中にを有効にし、サーバーからハブイベントに応答することができます。</span><span class="sxs-lookup"><span data-stu-id="bb010-130">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="bb010-131">初期化中に、 `ClockHubClient`によっての`HubConnection`インスタンスが作成さ`IClock.ShowTime`れ、そのメソッドがハブの`ShowTime`イベントのハンドラーとして有効になります。</span><span class="sxs-lookup"><span data-stu-id="bb010-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="bb010-132">`IHostedService.StartAsync`実装では、 `HubConnection`が非同期的に開始されます。</span><span class="sxs-lookup"><span data-stu-id="bb010-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="bb010-133">メソッドの`IHostedService.StopAsync`実行中、 `HubConnection`は非同期的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="bb010-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="bb010-134">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="bb010-134">Additional resources</span></span>

* [<span data-ttu-id="bb010-135">開始するには</span><span class="sxs-lookup"><span data-stu-id="bb010-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="bb010-136">ハブ</span><span class="sxs-lookup"><span data-stu-id="bb010-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="bb010-137">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="bb010-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="bb010-138">厳密に型指定されたハブ</span><span class="sxs-lookup"><span data-stu-id="bb010-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
