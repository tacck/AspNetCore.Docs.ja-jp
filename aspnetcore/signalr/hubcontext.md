---
title: SignalRHubContext
author: bradygaster
description: ASP.NET Core HubContext サービスを使用し SignalR て、ハブの外部からクライアントに通知を送信する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubcontext
ms.openlocfilehash: d38cb5b3e818879d3ec40a927acfdd69dc85377a
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819251"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="1ce62-103">ハブの外部からメッセージを送信する</span><span class="sxs-lookup"><span data-stu-id="1ce62-103">Send messages from outside a hub</span></span>

<span data-ttu-id="1ce62-104">作成者: [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="1ce62-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="1ce62-105">SignalRハブは、サーバーに接続されたクライアントにメッセージを送信するための中核的な抽象化です SignalR 。</span><span class="sxs-lookup"><span data-stu-id="1ce62-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="1ce62-106">また、サービスを使用して、アプリ内の他の場所からメッセージを送信することもでき `IHubContext` ます。</span><span class="sxs-lookup"><span data-stu-id="1ce62-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="1ce62-107">この記事では、にアクセスして、 SignalR `IHubContext` ハブの外部からクライアントに通知を送信する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1ce62-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="1ce62-108">[サンプル コードを表示またはダウンロードします](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(ダウンロード方法)](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="1ce62-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="1ce62-109">IHubContext のインスタンスを取得する</span><span class="sxs-lookup"><span data-stu-id="1ce62-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="1ce62-110">ASP.NET Core では SignalR 、依存関係の挿入によってのインスタンスにアクセスでき `IHubContext` ます。</span><span class="sxs-lookup"><span data-stu-id="1ce62-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="1ce62-111">のインスタンスは、 `IHubContext` コントローラー、ミドルウェア、またはその他の DI サービスに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="1ce62-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="1ce62-112">インスタンスを使用して、クライアントにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="1ce62-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="1ce62-113">これ SignalR は、GlobalHost を使用してへのアクセスを提供する ASP.NET 4.x とは異なり `IHubContext` ます。</span><span class="sxs-lookup"><span data-stu-id="1ce62-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="1ce62-114">ASP.NET Core には、このグローバルシングルトンの必要性を排除する依存関係挿入フレームワークがあります。</span><span class="sxs-lookup"><span data-stu-id="1ce62-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="1ce62-115">コントローラーで IHubContext のインスタンスを挿入する</span><span class="sxs-lookup"><span data-stu-id="1ce62-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="1ce62-116">のインスタンス `IHubContext` を、コンストラクターに追加することによって、コントローラーに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="1ce62-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="1ce62-117">では、のインスタンスにアクセスできるようになったので、ハブ `IHubContext` 自体と同じようにハブメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="1ce62-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="1ce62-118">ミドルウェアで IHubContext のインスタンスを取得する</span><span class="sxs-lookup"><span data-stu-id="1ce62-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="1ce62-119">次のよう `IHubContext` にミドルウェアパイプライン内のにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="1ce62-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="1ce62-120">ハブメソッドがクラスの外部から呼び出された場合 `Hub` 、呼び出しに関連付けられている呼び出し元はありません。</span><span class="sxs-lookup"><span data-stu-id="1ce62-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="1ce62-121">そのため、、、およびの各プロパティにアクセスすることはできません `ConnectionId` `Caller` `Others` 。</span><span class="sxs-lookup"><span data-stu-id="1ce62-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="1ce62-122">IHost から IHubContext のインスタンスを取得する</span><span class="sxs-lookup"><span data-stu-id="1ce62-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="1ce62-123">`IHubContext`Web ホストからへのアクセスは、サードパーティの依存関係挿入フレームワークを使用するなど、ASP.NET Core 外部の領域と統合する場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1ce62-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="1ce62-124">厳密に型指定された HubContext を挿入する</span><span class="sxs-lookup"><span data-stu-id="1ce62-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="1ce62-125">厳密に型指定された HubContext を挿入するには、ハブがから継承されていることを確認し `Hub<T>` ます。</span><span class="sxs-lookup"><span data-stu-id="1ce62-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="1ce62-126">ではなく、インターフェイスを使用して挿入 `IHubContext<THub, T>` `IHubContext<THub>` します。</span><span class="sxs-lookup"><span data-stu-id="1ce62-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a><span data-ttu-id="1ce62-127">関連リソース</span><span class="sxs-lookup"><span data-stu-id="1ce62-127">Related resources</span></span>

* [<span data-ttu-id="1ce62-128">開始するには</span><span class="sxs-lookup"><span data-stu-id="1ce62-128">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1ce62-129">取って代わり</span><span class="sxs-lookup"><span data-stu-id="1ce62-129">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1ce62-130">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="1ce62-130">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
