---
title: ASP.NET Core SignalR の概要
author: bradygaster
description: ASP.NET Core ライブラリを通じて、 SignalR リアルタイムの機能をアプリに簡単に追加する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 6f3cec83c9af5ec6e820db4a15061eddac613f36
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022174"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="254cc-103">ASP.NET Core SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="254cc-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="254cc-104">SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="254cc-104">What is SignalR?</span></span>

<span data-ttu-id="254cc-105">ASP.NET Core SignalR は、リアルタイムの web 機能をアプリに簡単に追加できるオープンソースライブラリです。</span><span class="sxs-lookup"><span data-stu-id="254cc-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="254cc-106">リアルタイム web 機能を使用すると、サーバー側のコードでクライアントにコンテンツを瞬時にプッシュできます。</span><span class="sxs-lookup"><span data-stu-id="254cc-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="254cc-107">最適な候補 SignalR :</span><span class="sxs-lookup"><span data-stu-id="254cc-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="254cc-108">サーバーからの頻繁な更新が必要なアプリ。</span><span class="sxs-lookup"><span data-stu-id="254cc-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="254cc-109">たとえば、ゲーム、ソーシャル ネットワーク、投票、オークション、マップ、GPS などのアプリです。</span><span class="sxs-lookup"><span data-stu-id="254cc-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="254cc-110">ダッシュボードと監視アプリ。</span><span class="sxs-lookup"><span data-stu-id="254cc-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="254cc-111">たとえば、会社のダッシュボード、売上の即時更新、トラベル アラートなどです。</span><span class="sxs-lookup"><span data-stu-id="254cc-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="254cc-112">コラボレーション アプリ。</span><span class="sxs-lookup"><span data-stu-id="254cc-112">Collaborative apps.</span></span> <span data-ttu-id="254cc-113">ホワイトボード アプリとチーム会議ソフトウェアは、コラボレーション アプリの例です。</span><span class="sxs-lookup"><span data-stu-id="254cc-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="254cc-114">通知を必要とするアプリ。</span><span class="sxs-lookup"><span data-stu-id="254cc-114">Apps that require notifications.</span></span> <span data-ttu-id="254cc-115">ソーシャル ネットワーク、電子メール、チャット、ゲーム、トラベル アラート、その他の多くのアプリは通知を使用します。</span><span class="sxs-lookup"><span data-stu-id="254cc-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="254cc-116">SignalRサーバー間の[リモートプロシージャ呼び出し (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)を作成するための API を提供します。</span><span class="sxs-lookup"><span data-stu-id="254cc-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="254cc-117">Rpc は、サーバー側 .NET Core コードからクライアントで JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="254cc-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="254cc-118">ASP.NET Core のの一部の機能を次に示し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="254cc-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="254cc-119">接続管理を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="254cc-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="254cc-120">接続されているすべてのクライアントに同時にメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="254cc-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="254cc-121">たとえば、チャットルームなどです。</span><span class="sxs-lookup"><span data-stu-id="254cc-121">For example, a chat room.</span></span>
* <span data-ttu-id="254cc-122">特定のクライアントまたはクライアントグループにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="254cc-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="254cc-123">増加するトラフィックを処理するようにスケーリングします。</span><span class="sxs-lookup"><span data-stu-id="254cc-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="254cc-124">ソースは、 [ SignalR GitHub のリポジトリ](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)でホストされています。</span><span class="sxs-lookup"><span data-stu-id="254cc-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="254cc-125">トランスポート</span><span class="sxs-lookup"><span data-stu-id="254cc-125">Transports</span></span>

<span data-ttu-id="254cc-126">SignalRは、リアルタイム通信を処理するための次の手法をサポートしています (正常なフォールバックの順序)。</span><span class="sxs-lookup"><span data-stu-id="254cc-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="254cc-127">WebSocket</span><span class="sxs-lookup"><span data-stu-id="254cc-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="254cc-128">サーバーから送信されたイベント</span><span class="sxs-lookup"><span data-stu-id="254cc-128">Server-Sent Events</span></span>
* <span data-ttu-id="254cc-129">長いポーリング</span><span class="sxs-lookup"><span data-stu-id="254cc-129">Long Polling</span></span>

<span data-ttu-id="254cc-130">SignalRでは、サーバーとクライアントの機能内にある最適なトランスポート方法が自動的に選択されます。</span><span class="sxs-lookup"><span data-stu-id="254cc-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="254cc-131">ハブ</span><span class="sxs-lookup"><span data-stu-id="254cc-131">Hubs</span></span>

<span data-ttu-id="254cc-132">SignalRは、*ハブ*を使用してクライアントとサーバー間の通信を行います。</span><span class="sxs-lookup"><span data-stu-id="254cc-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="254cc-133">ハブは、クライアントとサーバーが相互にメソッドを呼び出すことができるようにする、高レベルのパイプラインです。</span><span class="sxs-lookup"><span data-stu-id="254cc-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="254cc-134">SignalRコンピューターの境界を越えたディスパッチを自動的に処理します。これにより、クライアントはサーバーでメソッドを呼び出すことができ、その逆も可能になります。</span><span class="sxs-lookup"><span data-stu-id="254cc-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="254cc-135">厳密に型指定されたパラメーターをメソッドに渡すことができます。これにより、モデルバインドが有効になります。</span><span class="sxs-lookup"><span data-stu-id="254cc-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="254cc-136">SignalRには、JSON に基づくテキストプロトコルと[Messagepack](https://msgpack.org/)に基づくバイナリプロトコルという2つの組み込みのハブプロトコルが用意されています。</span><span class="sxs-lookup"><span data-stu-id="254cc-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="254cc-137">MessagePack では、通常、JSON と比較してより小さなメッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="254cc-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="254cc-138">以前のブラウザーでは、MessagePack プロトコルのサポートを提供するために、 [Xhr レベル 2](https://caniuse.com/#feat=xhr2)がサポートされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="254cc-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="254cc-139">ハブは、クライアント側のメソッドの名前とパラメーターを含むメッセージを送信することによって、クライアント側のコードを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="254cc-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="254cc-140">メソッドパラメーターとして送信されるオブジェクトは、構成されたプロトコルを使用して逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="254cc-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="254cc-141">クライアントは、クライアント側コードのメソッドと名前を一致させようとします。</span><span class="sxs-lookup"><span data-stu-id="254cc-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="254cc-142">クライアントが一致を検出すると、メソッドを呼び出し、逆シリアル化されたパラメーターデータに渡します。</span><span class="sxs-lookup"><span data-stu-id="254cc-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="254cc-143">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="254cc-143">Additional resources</span></span>

* [<span data-ttu-id="254cc-144">For ASP.NET Core を使ってみる SignalR</span><span class="sxs-lookup"><span data-stu-id="254cc-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="254cc-145">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="254cc-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="254cc-146">取って代わり</span><span class="sxs-lookup"><span data-stu-id="254cc-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="254cc-147">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="254cc-147">JavaScript client</span></span>](xref:signalr/javascript-client)
