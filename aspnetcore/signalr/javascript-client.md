---
title: ASP.NET Core SignalR JavaScript クライアント
author: bradygaster
description: ASP.NET Core SignalR JavaScript クライアントの概要について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 966e8e171752edb230f6da82203fd901b0fdeaab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768943"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript クライアント

作成者: [Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript クライアントライブラリを使用すると、開発者はサーバー側のハブコードを呼び出すことができます。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="install-the-signalr-client-package"></a>クライアントパッケージSignalRをインストールする

SignalR JavaScript クライアントライブラリは[npm](https://www.npmjs.com/)パッケージとして配信されます。 次のセクションでは、さまざまな方法でクライアントライブラリをインストールする方法について説明します。

### <a name="install-with-npm"></a>Npm を使用してインストールする

Visual Studio を使用している場合は、ルートフォルダーで、**パッケージマネージャーコンソール**から次のコマンドを実行します。 Visual Studio Code には、**統合ターミナル**から次のコマンドを実行します。

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm は、 *node_modules\\ *フォルダーにパッケージの内容をインストールします。 *Wwwroot\\lib*フォルダーの下に*signalr*という名前の新しいフォルダーを作成します。 *Signalr*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm は、 *node_modules\\ *フォルダーにパッケージの内容をインストールします。 *Wwwroot\\lib*フォルダーの下に*signalr*という名前の新しいフォルダーを作成します。 *Signalr*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。

::: moniker-end

要素でSignalR JavaScript クライアントを参照します。 `<script>` 次に例を示します。

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Content Delivery Network を使用する (CDN)

Npm の前提条件なしでクライアントライブラリを使用するには、CDN でホストされるクライアントライブラリのコピーを参照します。 次に例を示します。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

クライアントライブラリは、次の CDNs で利用できます。

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>LibMan でのインストール

[Libman](xref:client-side/libman/index)を使用すると、CDN でホストされているクライアントライブラリから特定のクライアントライブラリファイルをインストールできます。 たとえば、縮小版の JavaScript ファイルのみをプロジェクトに追加します。 その方法の詳細については、「[クライアントライブラリのSignalR追加](xref:tutorials/signalr#add-the-signalr-client-library)」を参照してください。

## <a name="connect-to-a-hub"></a>ハブへの接続

次のコードでは、接続を作成して開始します。 ハブの名前は大文字と小文字が区別されません。

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>クロスオリジン接続

通常、ブラウザーは、要求されたページと同じドメインからの接続を読み込みます。 ただし、別のドメインへの接続が必要になる場合もあります。

悪意のあるサイトが別のサイトから機密データを読み取れないようにするため、既定では、[クロスオリジン接続](xref:security/cors)は無効になっています。 クロスオリジン要求を許可するには、 `Startup`クラスで有効にします。

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>クライアントからのハブメソッドの呼び出し

JavaScript クライアントは、 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)の[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)メソッドを使用して、ハブでパブリックメソッドを呼び出します。 メソッド`invoke`は、次の2つの引数を受け取ります。

* ハブメソッドの名前。 次の例では、ハブのメソッド名は`SendMessage`です。
* ハブメソッドで定義されているすべての引数。 次の例では、引数名は`message`です。 このコード例では、Internet Explorer を除くすべての主要なブラウザーの現在のバージョンでサポートされている、矢印関数の構文を使用します。

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> *サーバーレスモード*で Azure SignalRサービスを使用している場合は、クライアントからハブメソッドを呼び出すことはできません。 詳細については、 [ SignalRサービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)を参照してください。

メソッド`invoke`は、JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。 は`Promise` 、サーバーのメソッドがを返すときに戻り値 (存在する場合) を使用して解決されます。 サーバー上のメソッドがエラーをスローした場合`Promise` 、はエラーメッセージと共に拒否されます。 これらの`then`ケース`catch` (または`Promise` `await`構文) を処理するには、自身でメソッドとメソッドを使用します。

メソッド`send`は、JavaScript `Promise`を返します。 は`Promise` 、メッセージがサーバーに送信されたときに解決されます。 メッセージの送信中にエラーが発生した`Promise`場合、はエラーメッセージと共に拒否されます。 これらの`then`ケース`catch` (または`Promise` `await`構文) を処理するには、自身でメソッドとメソッドを使用します。

> [!NOTE]
> を`send`使用すると、サーバーがメッセージを受信するまで待機しません。 このため、サーバーからデータまたはエラーを返すことはできません。

## <a name="call-client-methods-from-hub"></a>ハブからクライアントメソッドを呼び出す

ハブからメッセージを受信するには、の[on](/javascript/api/%40aspnet/signalr/hubconnection#on)メソッドを使用して`HubConnection`メソッドを定義します。

* JavaScript クライアントメソッドの名前。 次の例では、メソッド名は`ReceiveMessage`です。
* ハブがメソッドに渡す引数。 次の例では、引数の値`message`はです。

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

の`connection.on`前のコードは、サーバー側のコードが[sendasync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)メソッドを使用して呼び出したときに実行されます。

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRと`SendAsync` `connection.on`で定義されたメソッド名と引数を照合することによって、どのクライアントメソッドを呼び出すかを決定します。

> [!NOTE]
> ベストプラクティスとして、の後`HubConnection` `on`にの[start](/javascript/api/%40aspnet/signalr/hubconnection#start)メソッドを呼び出します。 これにより、メッセージを受信する前にハンドラーが確実に登録されます。

## <a name="error-handling-and-logging"></a>エラー処理とログ記録

メソッドを`catch` `start`メソッドの最後にチェーンして、クライアント側のエラーを処理します。 ブラウザー `console.error`のコンソールにエラーを出力するには、を使用します。

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

接続が確立されたときにログに記録する logger と種類のイベントを渡すことによって、クライアント側のログトレースを設定します。 メッセージは、指定されたログレベル以上でログに記録されます。 使用可能なログレベルは次のとおりです。

* `signalR.LogLevel.Error`&ndash;エラーメッセージ。 メッセージ`Error`のみをログに記録します。
* `signalR.LogLevel.Warning`&ndash;潜在的なエラーに関する警告メッセージ。 ログ`Warning`と`Error`メッセージ。
* `signalR.LogLevel.Information`&ndash;エラーのないステータスメッセージ。 、 `Information`、 `Warning`および`Error`の各メッセージをログに記録します。
* `signalR.LogLevel.Trace`&ndash;トレースメッセージ。 ハブとクライアント間で転送されたデータを含む、すべてをログに記録します。

ログレベルを構成するには、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)で[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッドを使用します。 メッセージは、ブラウザーコンソールに記録されます。

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>クライアントの再接続

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自動的に再接続する

のSignalR JavaScript クライアントは、 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)のメソッドを使用して自動的に再接続するように構成できます。 既定では、自動的に再接続されません。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

パラメーターを指定し`withAutomaticReconnect()`ない場合、は、再接続を試行する前に、それぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。

再接続の試行を開始する`HubConnection`前に、は`HubConnectionState.Reconnecting`状態に遷移し`onreconnecting` 、コールバックを起動し`Disconnected`ます。この状態`onclose`に遷移し`HubConnection` 、自動再接続が構成されていないなどのコールバックをトリガーします。 これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

最初の4回の試行でクライアントが正常に再`HubConnection`接続した場合、 `Connected`は状態に戻り`onreconnected` 、コールバックを起動します。 これにより、接続が再確立されたことをユーザーに通知することができます。

接続はサーバーにまったく新しいものであるため、 `connectionId` `onreconnected`コールバックに新しいが提供されます。

> [!WARNING]
> が`onreconnected`ネゴシエーションを`connectionId` [スキップ](xref:signalr/configuration#configure-client-options)するように構成さ`HubConnection`れている場合、コールバックのパラメーターは未定義になります。

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`最初の開始`HubConnection`エラーを再試行するようにを構成しません。そのため、開始エラーは手動で処理する必要があります。

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

最初の4回の試行でクライアントが正常に再接続`HubConnection`されない場合`Disconnected` 、は状態に遷移し、その[閉じる](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバックを起動します。 これにより、接続が完全に失われたことをユーザーに通知し、ページを最新の情報に更新することをお勧めします。

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

再接続のタイミングを切断または変更する前に、カスタムの再接続試行`withAutomaticReconnect`回数を構成するために、では、各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け取ります。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

前の例では`HubConnection` 、接続が失われた直後に再接続を開始するようにを構成しています。 これは、既定の構成にも当てはまります。

最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。

2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。

このカスタム動作は、既定の構成のように、別の30秒でもう一度再接続を試行するのではなく、3回目の再接続試行の失敗後に停止することで、既定の動作から再び逸脱します。

タイミングと自動再接続試行回数をさらに細かく制御する場合は、 `withAutomaticReconnect` `IRetryPolicy`インターフェイスを実装するオブジェクトを受け取ります。このインターフェイスには`nextRetryDelayInMilliseconds`、という名前の1つのメソッドがあります。

`nextRetryDelayInMilliseconds`型`RetryContext`の1つの引数を受け取ります。 に`RetryContext`は、、、 `previousRetryCount`および`elapsedMilliseconds`の`retryReason` 3 つの`number`プロパティ`number` `Error`があります。 最初の再接続を試行する`previousRetryCount`前`elapsedMilliseconds`に、との`retryReason`両方がゼロになり、は接続が失われる原因となったエラーになります。 再試行に失敗するたびに`previousRetryCount` 、が 1 `elapsedMilliseconds`ずつインクリメントされます。これまでの再接続にかかった時間 (ミリ秒単位) を`retryReason`反映して更新されます。このエラーは、最後の再接続の試行が失敗した原因となったエラーになります。

`nextRetryDelayInMilliseconds`は、次回の再接続が試行される前に待機するミリ秒数を`null`表す数値`HubConnection`を返すか、再接続を停止する必要がある場合はを返します。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

または、[手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。

::: moniker-end

### <a name="manually-reconnect"></a>手動で再接続する

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3.0 より前では、のSignalR JavaScript クライアントは自動的に再接続しません。 クライアントを手動で再接続するコードを記述する必要があります。

::: moniker-end

次のコードは、標準的な手動再接続アプローチを示しています。

1. 接続を開始する関数 (この場合`start`は関数) が作成されます。
1. 接続の`start` `onclose`イベントハンドラーで関数を呼び出します。

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

実際の実装では、指数バックオフを使用するか、指定された回数の再試行を行います。

## <a name="additional-resources"></a>その他のリソース

* [JavaScript API リファレンス](/javascript/api/?view=signalr-js-latest)
* [JavaScript のチュートリアル](xref:tutorials/signalr)
* [WebPack と TypeScript のチュートリアル](xref:tutorials/signalr-typescript-webpack)
* [ハブ](xref:signalr/hubs)
* [.NET クライアント](xref:signalr/dotnet-client)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
* [クロスオリジン要求 (CORS)](xref:security/cors)
* [Azure SignalRサービスのサーバーレスドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)
