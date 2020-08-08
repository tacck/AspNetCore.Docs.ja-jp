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
# <a name="no-locsignalr-api-design-considerations"></a>SignalRAPI の設計に関する考慮事項

By [Andrew Stanton-看護師](https://twitter.com/anurse)

この記事では、ベースの Api を構築するためのガイダンスを提供 SignalR します。

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>カスタムオブジェクトパラメーターを使用して下位互換性を確保する

SignalRクライアントまたはサーバー上のハブメソッドにパラメーターを追加することは、*互換性に影響する変更*点です。 これは、適切な数のパラメーターを指定せずにメソッドを呼び出そうとすると、古いクライアントまたはサーバーがエラーを受け取ることを意味します。 ただし、カスタムオブジェクトパラメーターにプロパティを追加することは、互換性に影響する変更点では**ありません**。 これは、クライアントまたはサーバーでの変更に対して回復力のある互換性のある Api を設計するために使用できます。

たとえば、次のようなサーバー側 API を考えてみます。

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

JavaScript クライアントは、次のようにを使用してこのメソッドを呼び出し `invoke` ます。

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

後でサーバーメソッドに2番目のパラメーターを追加した場合、古いクライアントはこのパラメーター値を提供しません。 例:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

古いクライアントがこのメソッドを呼び出そうとすると、次のようなエラーが表示されます。

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

サーバーでは、次のようなログメッセージが表示されます。

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

古いクライアントは1つのパラメーターのみを送信しましたが、新しいサーバー API では2つのパラメーターが必要でした。 カスタムオブジェクトをパラメーターとして使用すると、柔軟性が向上します。 カスタムオブジェクトを使用するように元の API を再設計してみましょう。

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

次に、クライアントはオブジェクトを使用してメソッドを呼び出します。

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

パラメーターを追加するのではなく、オブジェクトにプロパティを追加し `TotalLengthRequest` ます。

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

古いクライアントが1つのパラメーターを送信した場合、余分な `Param2` プロパティは残され `null` ます。 古いクライアントによって送信されたメッセージを検出するには、で `Param2` を確認 `null` し、既定値を適用します。 新しいクライアントは、両方のパラメーターを送信できます。

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

クライアントで定義されているメソッドでも、同じ手法が機能します。 サーバー側からカスタムオブジェクトを送信することができます。

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

クライアント側では、パラメーターを使用するの `Message` ではなく、プロパティにアクセスします。

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

後でメッセージの送信者をペイロードに追加する場合は、オブジェクトにプロパティを追加します。

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

古いクライアントは値を予期して `Sender` いないため、無視されます。 新しいクライアントは、を更新して新しいプロパティを読み取ることでそれを受け入れることができます。

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

この場合、新しいクライアントは、値を提供しない古いサーバーも許容され `Sender` ます。 古いサーバーでは値が提供されないため `Sender` 、クライアントは、アクセスする前に存在するかどうかを確認します。
