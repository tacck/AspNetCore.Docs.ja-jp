---
title: SignalRAPI の設計に関する考慮事項
author: anurse
description: SignalRアプリのバージョン間の互換性を確保するために api をデザインする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
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
uid: signalr/api-design
ms.openlocfilehash: 9ad8d30da552d3d3084534b8c7ca57386ad111ac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407799"
---
# <a name="signalr-api-design-considerations"></a>SignalR<span data-ttu-id="024dc-103">API の設計に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="024dc-103"> API design considerations</span></span>

<span data-ttu-id="024dc-104">By [Andrew Stanton-看護師](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="024dc-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="024dc-105">この記事では、ベースの Api を構築するためのガイダンスを提供 SignalR します。</span><span class="sxs-lookup"><span data-stu-id="024dc-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="024dc-106">カスタムオブジェクトパラメーターを使用して下位互換性を確保する</span><span class="sxs-lookup"><span data-stu-id="024dc-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="024dc-107">SignalRクライアントまたはサーバー上のハブメソッドにパラメーターを追加することは、*互換性に影響する変更*点です。</span><span class="sxs-lookup"><span data-stu-id="024dc-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="024dc-108">これは、適切な数のパラメーターを指定せずにメソッドを呼び出そうとすると、古いクライアントまたはサーバーがエラーを受け取ることを意味します。</span><span class="sxs-lookup"><span data-stu-id="024dc-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="024dc-109">ただし、カスタムオブジェクトパラメーターにプロパティを追加することは、互換性に影響する変更点では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="024dc-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="024dc-110">これは、クライアントまたはサーバーでの変更に対して回復力のある互換性のある Api を設計するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="024dc-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="024dc-111">たとえば、次のようなサーバー側 API を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="024dc-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="024dc-112">JavaScript クライアントは、次のようにを使用してこのメソッドを呼び出し `invoke` ます。</span><span class="sxs-lookup"><span data-stu-id="024dc-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="024dc-113">後でサーバーメソッドに2番目のパラメーターを追加した場合、古いクライアントはこのパラメーター値を提供しません。</span><span class="sxs-lookup"><span data-stu-id="024dc-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="024dc-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="024dc-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="024dc-115">古いクライアントがこのメソッドを呼び出そうとすると、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="024dc-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="024dc-116">サーバーでは、次のようなログメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="024dc-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="024dc-117">古いクライアントは1つのパラメーターのみを送信しましたが、新しいサーバー API では2つのパラメーターが必要でした。</span><span class="sxs-lookup"><span data-stu-id="024dc-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="024dc-118">カスタムオブジェクトをパラメーターとして使用すると、柔軟性が向上します。</span><span class="sxs-lookup"><span data-stu-id="024dc-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="024dc-119">カスタムオブジェクトを使用するように元の API を再設計してみましょう。</span><span class="sxs-lookup"><span data-stu-id="024dc-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="024dc-120">次に、クライアントはオブジェクトを使用してメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="024dc-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="024dc-121">パラメーターを追加するのではなく、オブジェクトにプロパティを追加し `TotalLengthRequest` ます。</span><span class="sxs-lookup"><span data-stu-id="024dc-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="024dc-122">古いクライアントが1つのパラメーターを送信した場合、余分な `Param2` プロパティは残され `null` ます。</span><span class="sxs-lookup"><span data-stu-id="024dc-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="024dc-123">古いクライアントによって送信されたメッセージを検出するには、で `Param2` を確認 `null` し、既定値を適用します。</span><span class="sxs-lookup"><span data-stu-id="024dc-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="024dc-124">新しいクライアントは、両方のパラメーターを送信できます。</span><span class="sxs-lookup"><span data-stu-id="024dc-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="024dc-125">クライアントで定義されているメソッドでも、同じ手法が機能します。</span><span class="sxs-lookup"><span data-stu-id="024dc-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="024dc-126">サーバー側からカスタムオブジェクトを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="024dc-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="024dc-127">クライアント側では、パラメーターを使用するの `Message` ではなく、プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="024dc-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="024dc-128">後でメッセージの送信者をペイロードに追加する場合は、オブジェクトにプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="024dc-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="024dc-129">古いクライアントは値を予期して `Sender` いないため、無視されます。</span><span class="sxs-lookup"><span data-stu-id="024dc-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="024dc-130">新しいクライアントは、を更新して新しいプロパティを読み取ることでそれを受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="024dc-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="024dc-131">この場合、新しいクライアントは、値を提供しない古いサーバーも許容され `Sender` ます。</span><span class="sxs-lookup"><span data-stu-id="024dc-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="024dc-132">古いサーバーでは値が提供されないため `Sender` 、クライアントは、アクセスする前に存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="024dc-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
