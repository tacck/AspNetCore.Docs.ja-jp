---
title: ASP.NET Core SignalR クライアント
author: bradygaster
description: さまざまな ASP.NET Core クライアントでサポートされている機能について説明 SignalR します。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/client-features
ms.openlocfilehash: a759e473ff7ffaebd0eb9309f37a959d0e06a466
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438963"
---
# <a name="aspnet-core-signalr-clients"></a>SignalR クライアントの ASP.NET Core

## <a name="versioning-support-and-compatibility"></a>バージョン管理、サポート、および互換性

SignalR クライアントは、サーバーコンポーネントと共に出荷され、一致するようにバージョンが付けられます。 サポートされている任意のクライアントは、サポートされている任意のサーバーに安全に接続できます。また、互換性の問題は修正されるものと見なされます。 SignalR クライアントは、.NET Core の残りの部分と同じサポートライフサイクルでサポートされています。 詳細について[は、.Net Core サポートポリシーを](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)参照してください。

多くの機能には、互換性のあるクライアント**と**サーバーが必要です。 さまざまな機能の最小バージョンを示す表については、以下を参照してください。

SignalR の1.x バージョンは、2.1 および 2.2 .NET Core リリースにマップされ、有効期間が設定されています。 バージョン3.x 以降では、SignalR バージョンは .NET の残りの部分と完全に一致し、サポートライフサイクルは同じです。

| SignalR のバージョン | .NET Core バージョン | サポート レベル | サポート終了 |
| - | - | - | - |
| 1.0.x | 2.1.x | 長期的なサポート | 2021年8月21日 |
| 1.1. x | 系 | サポート終了 | 2019年12月23日 |
| 3. x 以降 | *SignalR バージョンと同じ* | [.Net Core サポートポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)を参照してください。 |

**注:** ASP.NET Core 3.0 では、JavaScript クライアントは npm パッケージに*移動*しました `@microsoft/signalr` 。

## <a name="feature-distribution"></a>機能の配布

次の表は、リアルタイムサポートを提供するクライアントの機能とサポートを示しています。 各機能について、この機能をサポートする*最小*バージョンが一覧表示されます。 バージョンが表示されない場合、この機能はサポートされていません。

| 特徴 | Server (サーバー) | .NET クライアント | JavaScript クライアント | Java クライアント |
| ---- | :-: | :-: | :-: | :-: |
| Azure SignalR サービスのサポート |2.1.0|1.0.0|1.0.0|1.0.0|
| [サーバーからクライアントへのストリーミング](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [クライアントとサーバー間のストリーミング](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| 自動再接続 ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)、 [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Websocket トランスポート |2.1.0|1.0.0|1.0.0|1.0.0|
| サーバー送信イベントトランスポート |2.1.0|1.0.0|1.0.0|❌|
| 長いポーリングトランスポート |2.1.0|1.0.0|1.0.0|3.0.0|
| JSON ハブプロトコル |2.1.0|1.0.0|1.0.0|1.0.0|
| MessagePack ハブ プロトコル |2.1.0|1.0.0|1.0.0|❌|

その他のクライアント機能の有効化のサポートは[、microsoft の問題追跡ツール](https://github.com/dotnet/AspNetCore/issues)で追跡されます。

## <a name="additional-resources"></a>その他のリソース

* [For ASP.NET Core を使ってみる SignalR](xref:tutorials/signalr)
* [サポートされているプラットフォーム](xref:signalr/supported-platforms)
* [ハブ](xref:signalr/hubs)
* [JavaScript クライアント](xref:signalr/javascript-client)
