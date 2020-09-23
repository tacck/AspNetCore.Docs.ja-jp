---
title: 期限とキャンセルを使用した信頼性の高い gRPC サービス
author: jamesnk
description: .NET で期限とキャンセルを使用して信頼性の高い gRPC サービスを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
no-loc:
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: 59b737a032ea37a554ad5ddd0f4d44e4e1602d88
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594383"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>期限とキャンセルを使用した信頼性の高い gRPC サービス

作成者: [James Newton-King](https://twitter.com/jamesnk)

期限とキャンセルは、gRPC クライアントが実行中の呼び出しを中止するために使用する機能です。 この記事では、期限とキャンセルが重要である理由と、.NET gRPC アプリでそれらを使用する方法について説明します。

## <a name="deadlines"></a>期限

期限を使用すれば、gRPC クライアントで、呼び出しの完了をどれだけの時間待機するかを指定できます。 期限を超過した場合、呼び出しはキャンセルされます。 期限を設定することは重要です。呼び出しを実行できる時間の長さに上限を設けることができるからです。 これにより、誤動作しているサービスが永遠に実行されてサーバー リソースが枯渇するのを防ぐことができます。 期限は、信頼性の高いアプリをビルドするための便利なツールであり、構成する必要があります。

期限の構成:

* 期限は、呼び出しを行うときに `CallOptions.Deadline` を使用して構成します。
* 期限に既定値はありません。 期限が指定されない限り、gRPC の呼び出しに時間の制限はありません。
* 期限とは、期限を超過したときの UTC 時刻です。 たとえば、`DateTime.UtcNow.AddSeconds(5)` は今から 5 秒が期限です。
* 過去または現在の時刻が使用されている場合、呼び出しはすぐに期限を超過します。
* 期限は、gRPC 呼び出しと共にサービスに送信され、クライアントとサービスの両方によって個別に追跡されます。 gRPC の呼び出しは 1 台のコンピューター上で完了しますが、その応答がクライアントに返される前に期限を超過してしまう可能性があります。

期限を超過した場合、クライアントとサービスでは動作は異なります。

* クライアントでは、基になる HTTP 要求が直ちに中止され、`DeadlineExceeded` エラーがスローされます。 クライアント アプリでは、エラーをキャッチしてタイムアウト メッセージをユーザーに表示することを選択できます。
* サーバー上では、実行中の HTTP 要求が中止され、[ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) が発生します。 HTTP 要求は中止されますが、gRPC 呼び出しは、メソッドが完了するまでサーバー上で実行され続けます。 非同期メソッドにキャンセル トークンが渡され、呼び出しと共にキャンセルされるようにすることが重要です。 たとえば、非同期データベース クエリおよび HTTP 要求にキャンセル トークンを渡します。 キャンセル トークンを渡すことにより、キャンセルされた呼び出しをサーバー上ですばやく完了させ、他の呼び出しのためにリソースを解放することができます。

gRPC 呼び出しの期限を設定するには、`CallOptions.Deadline` を構成します。

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

gRPC サービスで `ServerCallContext.CancellationToken` を使用する場合:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>期限の伝達

実行中の gRPC サービスから gRPC 呼び出しが行われる場合は、期限が伝達される必要があります。 次に例を示します。

1. クライアント アプリで、期限付きの `FrontendService.GetUser` 呼び出しが行われています。
2. `FrontendService` は `UserService.GetUser` を呼び出します。 クライアントによって指定された期限は、新しい gRPC 呼び出しでも指定される必要があります。
3. `UserService.GetUser` は期限を受け取ります。 それは、クライアント アプリの期限を超過すると、正常にタイムアウトします。

呼び出しコンテキストでは、`ServerCallContext.Deadline` を使用して期限が指定されます。

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

手動による期限の伝達は煩雑になることがあります。 期限は、すべての呼び出しに渡す必要がありますが、誤って見逃しがちです。 gRPC クライアント ファクトリでは、自動ソリューションを使用できます。 `EnableCallContextPropagation` を指定すると、次のようになります。

* 期限およびキャンセル トークンが子呼び出しに自動的に伝達されます。
* 複雑で入れ子になった gRPC のシナリオで、常に期限とキャンセルが確実に伝達されるようにする優れた方法となります。

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

詳細については、「<xref:grpc/clientfactory#deadline-and-cancellation-propagation>」を参照してください。

## <a name="cancellation"></a>キャンセル

キャンセルを使用すると、gRPC クライアントで、必要なくなった実行時間の長い呼び出しをキャンセルすることができます。 たとえば、リアルタイムの更新をストリームする gRPC 呼び出しは、ユーザーが Web サイト上のページにアクセスしたときに開始されます。 ユーザーがそのページから移動したとき、ストリームはキャンセルされる必要があります。

クライアントで gRPC 呼び出しをキャンセルするには、[CallOptions CancellationToken](xref:System.Threading.CancellationToken) を使用してキャンセル トークンを渡すか、または呼び出しに対して `Dispose` を呼び出します。

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

キャンセルできる gRPC サービスは次のとおりです。
* `ServerCallContext.CancellationToken` を非同期メソッドに渡します。 非同期メソッドをキャンセルすると、サーバー上での呼び出しを迅速に完了させることができます。
* キャンセル トークンを子呼び出しに伝達します。 キャンセル トークンを伝達することにより、子の呼び出しがその親と共に確実にキャンセルされます。 [gRPC クライアント ファクトリ](xref:grpc/clientfactory)と `EnableCallContextPropagation()` を使用すれば、キャンセル トークンが自動的に伝達されます。

## <a name="additional-resources"></a>その他のリソース

* <xref:grpc/client>
* <xref:grpc/clientfactory>
