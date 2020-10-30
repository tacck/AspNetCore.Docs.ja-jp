---
title: ':::no-loc(SignalR)::: HubContext'
author: bradygaster
description: 'ASP.NET Core HubContext サービスを使用し :::no-loc(SignalR)::: て、ハブの外部からクライアントに通知を送信する方法について説明します。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058182"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="90bf8-103">ハブの外部からメッセージを送信する</span><span class="sxs-lookup"><span data-stu-id="90bf8-103">Send messages from outside a hub</span></span>

<span data-ttu-id="90bf8-104">作成者: [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="90bf8-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="90bf8-105">:::no-loc(SignalR):::ハブは、サーバーに接続されたクライアントにメッセージを送信するための中核的な抽象化です :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="90bf8-105">The :::no-loc(SignalR)::: hub is the core abstraction for sending messages to clients connected to the :::no-loc(SignalR)::: server.</span></span> <span data-ttu-id="90bf8-106">また、サービスを使用して、アプリ内の他の場所からメッセージを送信することもでき `IHubContext` ます。</span><span class="sxs-lookup"><span data-stu-id="90bf8-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="90bf8-107">この記事では、にアクセスして、 :::no-loc(SignalR)::: `IHubContext` ハブの外部からクライアントに通知を送信する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="90bf8-107">This article explains how to access a :::no-loc(SignalR)::: `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="90bf8-108">[サンプル コードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) ([ダウンロード方法](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="90bf8-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="90bf8-109">IHubContext のインスタンスを取得する</span><span class="sxs-lookup"><span data-stu-id="90bf8-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="90bf8-110">ASP.NET Core では :::no-loc(SignalR)::: 、依存関係の挿入によってのインスタンスにアクセスでき `IHubContext` ます。</span><span class="sxs-lookup"><span data-stu-id="90bf8-110">In ASP.NET Core :::no-loc(SignalR):::, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="90bf8-111">のインスタンスは、 `IHubContext` コントローラー、ミドルウェア、またはその他の DI サービスに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="90bf8-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="90bf8-112">インスタンスを使用して、クライアントにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="90bf8-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="90bf8-113">これ :::no-loc(SignalR)::: は、GlobalHost を使用してへのアクセスを提供する ASP.NET 4.x とは異なり `IHubContext` ます。</span><span class="sxs-lookup"><span data-stu-id="90bf8-113">This differs from ASP.NET 4.x :::no-loc(SignalR)::: which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="90bf8-114">ASP.NET Core には、このグローバルシングルトンの必要性を排除する依存関係挿入フレームワークがあります。</span><span class="sxs-lookup"><span data-stu-id="90bf8-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="90bf8-115">コントローラーで IHubContext のインスタンスを挿入する</span><span class="sxs-lookup"><span data-stu-id="90bf8-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="90bf8-116">のインスタンス `IHubContext` を、コンストラクターに追加することによって、コントローラーに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="90bf8-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="90bf8-117">では、のインスタンスにアクセスできるようになったので、ハブ `IHubContext` 自体と同じようにハブメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="90bf8-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="90bf8-118">ミドルウェアで IHubContext のインスタンスを取得する</span><span class="sxs-lookup"><span data-stu-id="90bf8-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="90bf8-119">次のよう `IHubContext` にミドルウェアパイプライン内のにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="90bf8-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

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
> <span data-ttu-id="90bf8-120">ハブメソッドがクラスの外部から呼び出された場合 `Hub` 、呼び出しに関連付けられている呼び出し元はありません。</span><span class="sxs-lookup"><span data-stu-id="90bf8-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="90bf8-121">そのため、、、およびの各プロパティにアクセスすることはできません `ConnectionId` `Caller` `Others` 。</span><span class="sxs-lookup"><span data-stu-id="90bf8-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="90bf8-122">IHost から IHubContext のインスタンスを取得する</span><span class="sxs-lookup"><span data-stu-id="90bf8-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="90bf8-123">`IHubContext`Web ホストからへのアクセスは、サードパーティの依存関係挿入フレームワークを使用するなど、ASP.NET Core 外部の領域と統合する場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="90bf8-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

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

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="90bf8-124">厳密に型指定された HubContext を挿入する</span><span class="sxs-lookup"><span data-stu-id="90bf8-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="90bf8-125">厳密に型指定された HubContext を挿入するには、ハブがから継承されていることを確認し `Hub<T>` ます。</span><span class="sxs-lookup"><span data-stu-id="90bf8-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="90bf8-126">ではなく、インターフェイスを使用して挿入 `IHubContext<THub, T>` `IHubContext<THub>` します。</span><span class="sxs-lookup"><span data-stu-id="90bf8-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

<span data-ttu-id="90bf8-127">詳細については、「 [厳密に型指定](xref:signalr/hubs#strongly-typed-hubs) されたハブ」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90bf8-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="90bf8-128">関連リソース</span><span class="sxs-lookup"><span data-stu-id="90bf8-128">Related resources</span></span>

* [<span data-ttu-id="90bf8-129">開始するには</span><span class="sxs-lookup"><span data-stu-id="90bf8-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="90bf8-130">ハブ</span><span class="sxs-lookup"><span data-stu-id="90bf8-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="90bf8-131">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="90bf8-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
