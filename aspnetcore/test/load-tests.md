---
title: ASP.NET Core のロード テスト/ストレス テスト
author: Jeremy-Meng
description: ASP.NET Core アプリのロード テストとストレス テストを行うために、いくつかの注目すべきツールとアプローチについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: 56f5a5caeea7581e26f8d8cec9662f439cd24b9e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060717"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="b7358-103">ASP.NET Core のロード テスト/ストレス テスト</span><span class="sxs-lookup"><span data-stu-id="b7358-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="b7358-104">ロード テストとストレス テストは、Web アプリのパフォーマンスと拡張性を確保するために重要です。</span><span class="sxs-lookup"><span data-stu-id="b7358-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="b7358-105">これらの目標は、よく似たテストを共有する場合でも異なります。</span><span class="sxs-lookup"><span data-stu-id="b7358-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="b7358-106">**ロード テスト**:アプリが応答の目標を満たしつつ、特定のシナリオで、指定されたユーザー負荷を処理できるかどうかをテストします。</span><span class="sxs-lookup"><span data-stu-id="b7358-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="b7358-107">アプリは通常の状態で実行されます。</span><span class="sxs-lookup"><span data-stu-id="b7358-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="b7358-108">**ストレス テスト**:厳しい条件下で、多くの場合長い時間実行したときのアプリの安定性をテストします。</span><span class="sxs-lookup"><span data-stu-id="b7358-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="b7358-109">このテストでは、急激な負荷または徐々に増加する負荷によって、アプリに高いユーザー負荷をかけます。または、アプリのコンピューティング リソースを制限します。</span><span class="sxs-lookup"><span data-stu-id="b7358-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="b7358-110">ストレス テストでは、負荷がかかっているアプリが障害から復旧し、期待される動作に適切に戻ることができるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="b7358-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="b7358-111">ストレスをかけるアプリは、通常の状況では実行されません。</span><span class="sxs-lookup"><span data-stu-id="b7358-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="b7358-112">Visual Studio 2019 では、[ロード テストを廃止](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)する計画が発表されました。</span><span class="sxs-lookup"><span data-stu-id="b7358-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="b7358-113">対応する Azure DevOps のクラウドベースのロード テスト サービスは終了しています。</span><span class="sxs-lookup"><span data-stu-id="b7358-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="b7358-114">サードパーティ製のツール</span><span class="sxs-lookup"><span data-stu-id="b7358-114">Third-party tools</span></span>

<span data-ttu-id="b7358-115">次の一覧には、さまざまな機能セットを備えたサードパーティ製の Web パフォーマンス ツールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b7358-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="b7358-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="b7358-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="b7358-117">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="b7358-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="b7358-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="b7358-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="b7358-119">k6</span><span class="sxs-lookup"><span data-stu-id="b7358-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="b7358-120">Locust</span><span class="sxs-lookup"><span data-stu-id="b7358-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="b7358-121">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="b7358-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="b7358-122">Netling</span><span class="sxs-lookup"><span data-stu-id="b7358-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="b7358-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="b7358-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)