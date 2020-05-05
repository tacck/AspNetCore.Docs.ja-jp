---
title: ASP.NET Core SignalR の概要
author: bradygaster
description: ASP.NET Core SignalRライブラリを通じて、リアルタイムの機能をアプリに簡単に追加する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 4e968659d23330c4b1403ab998dda9c9e6ebe080
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775779"
---
# <a name="introduction-to-aspnet-core-signalr"></a>ASP.NET Core SignalR の概要

## <a name="what-is-signalr"></a>SignalR概要

ASP.NET Core SignalRは、リアルタイムの web 機能をアプリに簡単に追加できるオープンソースライブラリです。 リアルタイム web 機能を使用すると、サーバー側のコードでクライアントにコンテンツを瞬時にプッシュできます。

最適な候補SignalR:

* サーバーからの頻繁な更新が必要なアプリ。 たとえば、ゲーム、ソーシャル ネットワーク、投票、オークション、マップ、GPS などのアプリです。
* ダッシュボードと監視アプリ。 たとえば、会社のダッシュボード、売上の即時更新、トラベル アラートなどです。
* コラボレーション アプリ。 ホワイトボード アプリとチーム会議ソフトウェアは、コラボレーション アプリの例です。
* 通知を必要とするアプリ。 ソーシャル ネットワーク、電子メール、チャット、ゲーム、トラベル アラート、その他の多くのアプリは通知を使用します。

SignalRサーバー間の[リモートプロシージャ呼び出し (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)を作成するための API を提供します。 Rpc は、サーバー側 .NET Core コードからクライアントで JavaScript 関数を呼び出します。

ASP.NET Core のの一部のSignalR機能を次に示します。

* 接続管理を自動的に処理します。
* 接続されているすべてのクライアントに同時にメッセージを送信します。 たとえば、チャットルームなどです。
* 特定のクライアントまたはクライアントグループにメッセージを送信します。
* 増加するトラフィックを処理するようにスケーリングします。

ソースは、 [ SignalR GitHub のリポジトリ](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)でホストされています。

## <a name="transports"></a>トランスポート

SignalRは、リアルタイム通信を処理するための次の手法をサポートしています (正常なフォールバックの順序)。

* [WebSocket](https://tools.ietf.org/html/rfc7118)
* サーバーから送信されたイベント
* 長いポーリング

SignalRでは、サーバーとクライアントの機能内にある最適なトランスポート方法が自動的に選択されます。

## <a name="hubs"></a>ハブ

SignalRは、*ハブ*を使用してクライアントとサーバー間の通信を行います。

ハブは、クライアントとサーバーが相互にメソッドを呼び出すことができるようにする、高レベルのパイプラインです。 SignalRコンピューターの境界を越えたディスパッチを自動的に処理します。これにより、クライアントはサーバーでメソッドを呼び出すことができ、その逆も可能になります。 厳密に型指定されたパラメーターをメソッドに渡すことができます。これにより、モデルバインドが有効になります。 SignalRには、JSON に基づくテキストプロトコルと[Messagepack](https://msgpack.org/)に基づくバイナリプロトコルという2つの組み込みのハブプロトコルが用意されています。  MessagePack では、通常、JSON と比較してより小さなメッセージが作成されます。 以前のブラウザーでは、MessagePack プロトコルのサポートを提供するために、 [Xhr レベル 2](https://caniuse.com/#feat=xhr2)がサポートされている必要があります。

ハブは、クライアント側のメソッドの名前とパラメーターを含むメッセージを送信することによって、クライアント側のコードを呼び出します。 メソッドパラメーターとして送信されるオブジェクトは、構成されたプロトコルを使用して逆シリアル化されます。 クライアントは、クライアント側コードのメソッドと名前を一致させようとします。 クライアントが一致を検出すると、メソッドを呼び出し、逆シリアル化されたパラメーターデータに渡します。

## <a name="additional-resources"></a>その他のリソース

* [For ASP.NET Core をSignalR使ってみる](xref:tutorials/signalr)
* [サポートされているプラットフォーム](xref:signalr/supported-platforms)
* [ハブ](xref:signalr/hubs)
* [JavaScript クライアント](xref:signalr/javascript-client)
