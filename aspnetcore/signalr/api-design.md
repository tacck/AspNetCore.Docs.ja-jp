---
title: SignalRAPI の設計に関する考慮事項
author: anurse
description: SignalRアプリのバージョン間の互換性を確保するために api をデザインする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: ef0285c611bd41d7fe686a4b370b6daae9be9174
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018989"
---
# <a name="no-locsignalr-api-design-considerations"></a><span data-ttu-id="852b1-103">SignalRAPI の設計に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="852b1-103">SignalR API design considerations</span></span>

<span data-ttu-id="852b1-104">By [Andrew Stanton-看護師](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="852b1-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="852b1-105">この記事では、ベースの Api を構築するためのガイダンスを提供 SignalR します。</span><span class="sxs-lookup"><span data-stu-id="852b1-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="852b1-106">カスタムオブジェクトパラメーターを使用して下位互換性を確保する</span><span class="sxs-lookup"><span data-stu-id="852b1-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="852b1-107">SignalRクライアントまたはサーバー上のハブメソッドにパラメーターを追加することは、*互換性に影響する変更*点です。</span><span class="sxs-lookup"><span data-stu-id="852b1-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="852b1-108">これは、適切な数のパラメーターを指定せずにメソッドを呼び出そうとすると、古いクライアントまたはサーバーがエラーを受け取ることを意味します。</span><span class="sxs-lookup"><span data-stu-id="852b1-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="852b1-109">ただし、カスタムオブジェクトパラメーターにプロパティを追加することは、互換性に影響する変更点では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="852b1-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="852b1-110">これは、クライアントまたはサーバーでの変更に対して回復力のある互換性のある Api を設計するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="852b1-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="852b1-111">たとえば、次のようなサーバー側 API を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="852b1-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="852b1-112">JavaScript クライアントは、次のようにを使用してこのメソッドを呼び出し `invoke` ます。</span><span class="sxs-lookup"><span data-stu-id="852b1-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="852b1-113">後でサーバーメソッドに2番目のパラメーターを追加した場合、古いクライアントはこのパラメーター値を提供しません。</span><span class="sxs-lookup"><span data-stu-id="852b1-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="852b1-114">例:</span><span class="sxs-lookup"><span data-stu-id="852b1-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="852b1-115">古いクライアントがこのメソッドを呼び出そうとすると、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="852b1-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="852b1-116">サーバーでは、次のようなログメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="852b1-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="852b1-117">古いクライアントは1つのパラメーターのみを送信しましたが、新しいサーバー API では2つのパラメーターが必要でした。</span><span class="sxs-lookup"><span data-stu-id="852b1-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="852b1-118">カスタムオブジェクトをパラメーターとして使用すると、柔軟性が向上します。</span><span class="sxs-lookup"><span data-stu-id="852b1-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="852b1-119">カスタムオブジェクトを使用するように元の API を再設計してみましょう。</span><span class="sxs-lookup"><span data-stu-id="852b1-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="852b1-120">次に、クライアントはオブジェクトを使用してメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="852b1-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="852b1-121">パラメーターを追加するのではなく、オブジェクトにプロパティを追加し `TotalLengthRequest` ます。</span><span class="sxs-lookup"><span data-stu-id="852b1-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="852b1-122">古いクライアントが1つのパラメーターを送信した場合、余分な `Param2` プロパティは残され `null` ます。</span><span class="sxs-lookup"><span data-stu-id="852b1-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="852b1-123">古いクライアントによって送信されたメッセージを検出するには、で `Param2` を確認 `null` し、既定値を適用します。</span><span class="sxs-lookup"><span data-stu-id="852b1-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="852b1-124">新しいクライアントは、両方のパラメーターを送信できます。</span><span class="sxs-lookup"><span data-stu-id="852b1-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="852b1-125">クライアントで定義されているメソッドでも、同じ手法が機能します。</span><span class="sxs-lookup"><span data-stu-id="852b1-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="852b1-126">サーバー側からカスタムオブジェクトを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="852b1-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="852b1-127">クライアント側では、パラメーターを使用するの `Message` ではなく、プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="852b1-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="852b1-128">後でメッセージの送信者をペイロードに追加する場合は、オブジェクトにプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="852b1-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="852b1-129">古いクライアントは値を予期して `Sender` いないため、無視されます。</span><span class="sxs-lookup"><span data-stu-id="852b1-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="852b1-130">新しいクライアントは、を更新して新しいプロパティを読み取ることでそれを受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="852b1-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="852b1-131">この場合、新しいクライアントは、値を提供しない古いサーバーも許容され `Sender` ます。</span><span class="sxs-lookup"><span data-stu-id="852b1-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="852b1-132">古いサーバーでは値が提供されないため `Sender` 、クライアントは、アクセスする前に存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="852b1-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
