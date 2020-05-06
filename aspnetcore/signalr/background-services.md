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
# <a name="host-aspnet-core-signalr-in-background-services"></a>バックグラウンドサービスSignalRでのホスト ASP.NET Core

[Brady](https://twitter.com/bradygaster)による

この記事では、次のガイダンスを提供します。

* ASP.NET Core SignalRでホストされたバックグラウンドワーカープロセスを使用してハブをホストする。
* .NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)内から接続されたクライアントにメッセージを送信する。

[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>スタートアップSignalRで有効にする

::: moniker range=">= aspnetcore-3.0"

バックグラウンドワーカー SignalRプロセスのコンテキストで ASP.NET Core ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。 `Startup.ConfigureServices`メソッドでを呼び出す`services.AddSignalR`と、をサポートSignalRするために必要なサービスが ASP.NET Core 依存関係挿入 (DI) 層に追加されます。 で`Startup.Configure`は、 `MapHub`メソッドが`UseEndpoints`コールバックで呼び出され、ASP.NET Core 要求パイプラインのハブエンドポイントに接続します。

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

バックグラウンドワーカー SignalRプロセスのコンテキストで ASP.NET Core ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。 `Startup.ConfigureServices`メソッドでを呼び出す`services.AddSignalR`と、をサポートSignalRするために必要なサービスが ASP.NET Core 依存関係挿入 (DI) 層に追加されます。 で`Startup.Configure`は、 `UseSignalR`メソッドを呼び出して、ASP.NET Core 要求パイプラインのハブエンドポイントを接続します。

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

前の例では、 `ClockHub`クラスは、 `Hub<T>`厳密に型指定されたハブを作成するクラスを実装しています。 は`ClockHub` 、エンドポイント`/hubs/clock`での`Startup`要求に応答するようにクラスで構成されています。

厳密に型指定されたハブの詳細については、「 [ SignalRのハブを使用](xref:signalr/hubs#strongly-typed-hubs)した ASP.NET Core」を参照してください。

> [!NOTE]
> この機能は[\<ハブ t>](xref:Microsoft.AspNetCore.SignalR.Hub`1)クラスに限定されません。 また、 [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)など、[ハブ](xref:Microsoft.AspNetCore.SignalR.Hub)から継承されるすべてのクラスも機能します。

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

厳密に型指定`ClockHub`されたによっ`IClock`て使用されるインターフェイスは、インターフェイスです。

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>バックグラウンドサービスSignalRからハブを呼び出す

起動時に、 `Worker`クラス`BackgroundService`はを使用`AddHostedService`して有効になります。

```csharp
services.AddHostedService<Worker>();
```

はSignalR `Startup`フェーズ中にも有効になるため、各ハブは ASP.NET Core の HTTP 要求パイプライン内の個々のエンドポイントにアタッチされます。各ハブ`IHubContext<T>`は、サーバー上のによって表されます。 ASP.NET Core の DI 機能を使用して、 `BackgroundService`クラス、MVC コントローラークラス、 Razorページモデルなどのホスト層によってインスタンス化された他のクラスは、構築中に`IHubContext<ClockHub, IClock>`のインスタンスを受け入れることによって、サーバー側ハブへの参照を取得できます。

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

バックグラウンドサービス`ExecuteAsync`でメソッドが繰り返し呼び出されると、サーバーの現在の日付と時刻が、 `ClockHub`を使用して接続されたクライアントに送信されます。

## <a name="react-to-signalr-events-with-background-services"></a>バックグラウンドサービスSignalRを使用したイベントへの対応

用SignalRの JavaScript クライアントを使用するシングルページアプリの場合と同様に、.net デスクトップアプリでは<xref:signalr/dotnet-client>を使用`BackgroundService`し`IHostedService`てを使用できますが、またSignalRはの実装を使用してハブに接続し、イベントに応答することもできます。

クラス`ClockHubClient`は、インターフェイスと`IClock` `IHostedService`インターフェイスの両方を実装します。 このようにして、の`Startup`実行中にを有効にし、サーバーからハブイベントに応答することができます。

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

初期化中に、 `ClockHubClient`によっての`HubConnection`インスタンスが作成さ`IClock.ShowTime`れ、そのメソッドがハブの`ShowTime`イベントのハンドラーとして有効になります。

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

`IHostedService.StartAsync`実装では、 `HubConnection`が非同期的に開始されます。

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

メソッドの`IHostedService.StopAsync`実行中、 `HubConnection`は非同期的に破棄されます。

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>その他のリソース

* [開始するには](xref:tutorials/signalr)
* [ハブ](xref:signalr/hubs)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
* [厳密に型指定されたハブ](xref:signalr/hubs#strongly-typed-hubs)
