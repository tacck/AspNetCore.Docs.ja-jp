---
title: ASP.NET Core SignalR 接続のトラブルシューティング
author: bradygaster
description: SignalR接続のトラブルシューティングを ASP.NET Core します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059612"
---
# <a name="troubleshoot-connection-errors"></a>接続エラーのトラブルシューティング

このセクションでは、ASP.NET Core ハブへの接続を確立しようとしたときに発生する可能性があるエラーについて説明 SignalR します。

### <a name="response-code-404"></a>応答コード404

Websocket およびを使用する場合 `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* 固定セッションを使用せずに複数のサーバーを使用する場合は、1台のサーバーで接続を開始してから別のサーバーに切り替えることができます。 もう一方のサーバーは、以前の接続を認識していません。
* クライアントが正しいエンドポイントに接続していることを確認します。 たとえば、サーバーがでホストされ、 `http://127.0.0.1:5000/hub/myHub` クライアントがに接続しようとしているとし `http://127.0.0.1:5000/myHub` ます。
* 接続で ID が使用され、ネゴシエート後にサーバーに要求を送信するのに時間がかかりすぎる場合、サーバーは次のようになります。

  * ID を削除します。
  * は404を返します。

### <a name="response-code-400-or-503"></a>応答コード400または503

次のエラーが発生した場合:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

このエラーは通常、Websocket トランスポートのみを使用しているクライアントによって発生しますが、Websocket プロトコルはサーバーで有効になっていません。

### <a name="response-code-307"></a>応答コード307

Websocket およびを使用する場合 `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

このエラーは、negotiate 要求中にも発生する可能性があります。

一般的な原因:
* アプリは、でを呼び出して HTTPS を強制する `UseHttpsRedirection` `Startup` か、URL 書き換えルールを使用して https を強制するように構成されています。

解決方法:
* クライアント側の URL を "http" から "https" に変更します。 `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>応答コード405

Http 状態コード 405-メソッドは使用できません

* アプリで [CORS](xref:signalr/security#cross-origin-resource-sharing) が有効になっていません

### <a name="response-code-0"></a>応答コード0

Http 状態コード 0-通常は [CORS](xref:signalr/security#cross-origin-resource-sharing) の問題です。状態コードは指定されていません。

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* 予想されるオリジンをに追加します。 `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* `.AllowCredentials()`CORS ポリシーにを追加します。 `.AllowAnyOrigin()`またはを `.WithOrigins("*")` このオプションと共に使用することはできません。

### <a name="response-code-413"></a>応答コード413

Http 状態コード 413-ペイロードが大きすぎます

多くの場合、アクセストークンが4k を超えていることが原因です。

* Azure サービスを使用している場合は、 SignalR サービスを介して送信される要求を次のようにカスタマイズすることによって、トークンサイズを減らします。
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>一時的なネットワークエラー

一時的なネットワークエラーによって接続が閉じられる場合があり SignalR ます。 サーバーは、終了した接続を正常なクライアント切断として解釈する場合があります。 クライアントが切断された理由に関する詳細情報を取得するには [、クライアントとサーバーからログを収集](xref:signalr/diagnostics)します。