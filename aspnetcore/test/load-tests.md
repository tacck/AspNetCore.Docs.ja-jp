---
title: ASP.NET Core のロード テスト/ストレス テスト
author: Jeremy-Meng
description: ASP.NET Core アプリのロード テストとストレス テストを行うために、いくつかの注目すべきツールとアプローチについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: f0c930a7689c7288a58cfc3ca630d3cd324bf2cd
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106807"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core のロード テスト/ストレス テスト

ロード テストとストレス テストは、Web アプリのパフォーマンスと拡張性を確保するために重要です。 これらの目標は、よく似たテストを共有する場合でも異なります。

**ロード テスト**:アプリが応答の目標を満たしつつ、特定のシナリオで、指定されたユーザー負荷を処理できるかどうかをテストします。 アプリは通常の状態で実行されます。

**ストレス テスト**:厳しい条件下で、多くの場合長い時間実行したときのアプリの安定性をテストします。 このテストでは、急激な負荷または徐々に増加する負荷によって、アプリに高いユーザー負荷をかけます。または、アプリのコンピューティング リソースを制限します。

ストレス テストでは、負荷がかかっているアプリが障害から復旧し、期待される動作に適切に戻ることができるかどうかを判断します。 ストレスをかけるアプリは、通常の状況では実行されません。

Visual Studio 2019 は、ロード テスト機能を備えた最後のバージョンの Visual Studio です。 今後、ロード テスト ツールを必要とするお客様には、Apache JMeter、Akamai CloudTest、BlazeMeter などの代替のツールを推奨します。 詳細については、「[Visual Studio 2019 リリース ノート](/visualstudio/releases/2019/release-notes-v16.0#test-tools)」を参照してください。

## <a name="visual-studio-tools"></a>Visual Studio ツール

ユーザーは、Visual Studio を使用して Web パフォーマンス テストおよびロード テストを作成、開発、およびデバッグできます。 Web ブラウザーでアクションを記録してテストを作成するオプションを使用できます。

Visual Studio 2017 を使用してロード テスト プロジェクトを作成、構成、および実行する方法については、「[クイックスタート: ロード テスト プロジェクトを作成する](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)」を参照してください。

ロード テストは、オンプレミスで実行するように構成することも、Azure DevOps を使用してクラウドで実行するように構成することもできます。

## <a name="third-party-tools"></a>サードパーティ製のツール

次の一覧には、さまざまな機能セットを備えたサードパーティ製の Web パフォーマンス ツールが含まれています。

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

