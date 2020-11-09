---
title: 期限とキャンセルを使用した信頼性の高い gRPC サービス
author: jamesnk
description: .NET で期限とキャンセルを使用して信頼性の高い gRPC サービスを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059924"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="93c4d-103">期限とキャンセルを使用した信頼性の高い gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="93c4d-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="93c4d-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="93c4d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="93c4d-105">期限とキャンセルは、gRPC クライアントが実行中の呼び出しを中止するために使用する機能です。</span><span class="sxs-lookup"><span data-stu-id="93c4d-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="93c4d-106">この記事では、期限とキャンセルが重要である理由と、.NET gRPC アプリでそれらを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="93c4d-107">期限</span><span class="sxs-lookup"><span data-stu-id="93c4d-107">Deadlines</span></span>

<span data-ttu-id="93c4d-108">期限を使用すれば、gRPC クライアントで、呼び出しの完了をどれだけの時間待機するかを指定できます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="93c4d-109">期限を超過した場合、呼び出しはキャンセルされます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="93c4d-110">期限を設定することは重要です。呼び出しを実行できる時間の長さに上限を設けることができるからです。</span><span class="sxs-lookup"><span data-stu-id="93c4d-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="93c4d-111">これにより、誤動作しているサービスが永遠に実行されてサーバー リソースが枯渇するのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="93c4d-112">期限は、信頼性の高いアプリをビルドするための便利なツールであり、構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="93c4d-113">期限の構成:</span><span class="sxs-lookup"><span data-stu-id="93c4d-113">Deadline configuration:</span></span>

* <span data-ttu-id="93c4d-114">期限は、呼び出しを行うときに `CallOptions.Deadline` を使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="93c4d-115">期限に既定値はありません。</span><span class="sxs-lookup"><span data-stu-id="93c4d-115">There is no default deadline value.</span></span> <span data-ttu-id="93c4d-116">期限が指定されない限り、gRPC の呼び出しに時間の制限はありません。</span><span class="sxs-lookup"><span data-stu-id="93c4d-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="93c4d-117">期限とは、期限を超過したときの UTC 時刻です。</span><span class="sxs-lookup"><span data-stu-id="93c4d-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="93c4d-118">たとえば、`DateTime.UtcNow.AddSeconds(5)` は今から 5 秒が期限です。</span><span class="sxs-lookup"><span data-stu-id="93c4d-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="93c4d-119">過去または現在の時刻が使用されている場合、呼び出しはすぐに期限を超過します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="93c4d-120">期限は、gRPC 呼び出しと共にサービスに送信され、クライアントとサービスの両方によって個別に追跡されます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="93c4d-121">gRPC の呼び出しは 1 台のコンピューター上で完了しますが、その応答がクライアントに返される前に期限を超過してしまう可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="93c4d-122">期限を超過した場合、クライアントとサービスでは動作は異なります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="93c4d-123">クライアントでは、基になる HTTP 要求が直ちに中止され、`DeadlineExceeded` エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="93c4d-124">クライアント アプリでは、エラーをキャッチしてタイムアウト メッセージをユーザーに表示することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="93c4d-125">サーバー上では、実行中の HTTP 要求が中止され、[ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) が発生します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="93c4d-126">HTTP 要求は中止されますが、gRPC 呼び出しは、メソッドが完了するまでサーバー上で実行され続けます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="93c4d-127">非同期メソッドにキャンセル トークンが渡され、呼び出しと共にキャンセルされるようにすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="93c4d-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="93c4d-128">たとえば、非同期データベース クエリおよび HTTP 要求にキャンセル トークンを渡します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="93c4d-129">キャンセル トークンを渡すことにより、キャンセルされた呼び出しをサーバー上ですばやく完了させ、他の呼び出しのためにリソースを解放することができます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="93c4d-130">gRPC 呼び出しの期限を設定するには、`CallOptions.Deadline` を構成します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="93c4d-131">gRPC サービスで `ServerCallContext.CancellationToken` を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="93c4d-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="93c4d-132">期限の伝達</span><span class="sxs-lookup"><span data-stu-id="93c4d-132">Propagating deadlines</span></span>

<span data-ttu-id="93c4d-133">実行中の gRPC サービスから gRPC 呼び出しが行われる場合は、期限が伝達される必要があります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="93c4d-134">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-134">For example:</span></span>

1. <span data-ttu-id="93c4d-135">クライアント アプリで、期限付きの `FrontendService.GetUser` 呼び出しが行われています。</span><span class="sxs-lookup"><span data-stu-id="93c4d-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="93c4d-136">`FrontendService` は `UserService.GetUser` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="93c4d-137">クライアントによって指定された期限は、新しい gRPC 呼び出しでも指定される必要があります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="93c4d-138">`UserService.GetUser` は期限を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="93c4d-139">それは、クライアント アプリの期限を超過すると、正常にタイムアウトします。</span><span class="sxs-lookup"><span data-stu-id="93c4d-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="93c4d-140">呼び出しコンテキストでは、`ServerCallContext.Deadline` を使用して期限が指定されます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="93c4d-141">手動による期限の伝達は煩雑になることがあります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="93c4d-142">期限は、すべての呼び出しに渡す必要がありますが、誤って見逃しがちです。</span><span class="sxs-lookup"><span data-stu-id="93c4d-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="93c4d-143">gRPC クライアント ファクトリでは、自動ソリューションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="93c4d-144">`EnableCallContextPropagation` を指定すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="93c4d-145">期限およびキャンセル トークンが子呼び出しに自動的に伝達されます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="93c4d-146">複雑で入れ子になった gRPC のシナリオで、常に期限とキャンセルが確実に伝達されるようにする優れた方法となります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="93c4d-147">詳細については、「<xref:grpc/clientfactory#deadline-and-cancellation-propagation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93c4d-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="93c4d-148">キャンセル</span><span class="sxs-lookup"><span data-stu-id="93c4d-148">Cancellation</span></span>

<span data-ttu-id="93c4d-149">キャンセルを使用すると、gRPC クライアントで、必要なくなった実行時間の長い呼び出しをキャンセルすることができます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="93c4d-150">たとえば、リアルタイムの更新をストリームする gRPC 呼び出しは、ユーザーが Web サイト上のページにアクセスしたときに開始されます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="93c4d-151">ユーザーがそのページから移動したとき、ストリームはキャンセルされる必要があります。</span><span class="sxs-lookup"><span data-stu-id="93c4d-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="93c4d-152">クライアントで gRPC 呼び出しをキャンセルするには、[CallOptions CancellationToken](xref:System.Threading.CancellationToken) を使用してキャンセル トークンを渡すか、または呼び出しに対して `Dispose` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="93c4d-153">キャンセルできる gRPC サービスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="93c4d-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="93c4d-154">`ServerCallContext.CancellationToken` を非同期メソッドに渡します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="93c4d-155">非同期メソッドをキャンセルすると、サーバー上での呼び出しを迅速に完了させることができます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="93c4d-156">キャンセル トークンを子呼び出しに伝達します。</span><span class="sxs-lookup"><span data-stu-id="93c4d-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="93c4d-157">キャンセル トークンを伝達することにより、子の呼び出しがその親と共に確実にキャンセルされます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="93c4d-158">[gRPC クライアント ファクトリ](xref:grpc/clientfactory)と `EnableCallContextPropagation()` を使用すれば、キャンセル トークンが自動的に伝達されます。</span><span class="sxs-lookup"><span data-stu-id="93c4d-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93c4d-159">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="93c4d-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
