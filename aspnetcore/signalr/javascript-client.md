---
title: 'ASP.NET Core SignalR JavaScript クライアント'
author: bradygaster
description: 'ASP.NET Core JavaScript クライアントの概要について説明 SignalR します。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: b4b1bc6131a6676710adbf2503efe3f304d89a58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050850"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="ff10a-103">ASP.NET Core SignalR JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="ff10a-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff10a-104">作成者: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="ff10a-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="ff10a-105">ASP.NET Core SignalR JavaScript クライアントライブラリを使用すると、開発者はサーバー側のハブコードを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="ff10a-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ff10a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="ff10a-107">クライアントパッケージをインストールする SignalR</span><span class="sxs-lookup"><span data-stu-id="ff10a-107">Install the SignalR client package</span></span>

<span data-ttu-id="ff10a-108">SignalRJavaScript クライアントライブラリは[npm](https://www.npmjs.com/)パッケージとして配信されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="ff10a-109">次のセクションでは、さまざまな方法でクライアントライブラリをインストールする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="ff10a-110">Npm を使用してインストールする</span><span class="sxs-lookup"><span data-stu-id="ff10a-110">Install with npm</span></span>

<span data-ttu-id="ff10a-111">Visual Studio の場合は、ルートフォルダーで、 **パッケージマネージャーコンソール** から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="ff10a-112">Visual Studio Code には、 **統合ターミナル** から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-112">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="ff10a-113">npm は、 *node_modules \\ @microsoft\signalr\dist\browser* フォルダーにパッケージの内容をインストールします。</span><span class="sxs-lookup"><span data-stu-id="ff10a-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="ff10a-114">*Wwwroot \\ lib* フォルダーの下に *signalr* という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="ff10a-115">*signalr.js* ファイルを *wwwroot\lib\signalr* フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="ff10a-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="ff10a-116">SignalR要素で JavaScript クライアントを参照し `<script>` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="ff10a-117">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="ff10a-118">Content Delivery Network を使用する (CDN)</span><span class="sxs-lookup"><span data-stu-id="ff10a-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="ff10a-119">Npm の前提条件なしでクライアントライブラリを使用するには、CDN でホストされるクライアントライブラリのコピーを参照します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="ff10a-120">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="ff10a-121">クライアントライブラリは、次の CDNs で利用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="ff10a-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="ff10a-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="ff10a-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="ff10a-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="ff10a-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="ff10a-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="ff10a-125">LibMan でのインストール</span><span class="sxs-lookup"><span data-stu-id="ff10a-125">Install with LibMan</span></span>

<span data-ttu-id="ff10a-126">[Libman](xref:client-side/libman/index) を使用すると、CDN でホストされているクライアントライブラリから特定のクライアントライブラリファイルをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="ff10a-127">たとえば、縮小版の JavaScript ファイルのみをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="ff10a-128">その方法の詳細については、「 [ SignalR クライアントライブラリの追加](xref:tutorials/signalr#add-the-signalr-client-library)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10a-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="ff10a-129">ハブへの接続</span><span class="sxs-lookup"><span data-stu-id="ff10a-129">Connect to a hub</span></span>

<span data-ttu-id="ff10a-130">次のコードでは、接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="ff10a-131">ハブの名前では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="ff10a-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="ff10a-132">クロスオリジン接続</span><span class="sxs-lookup"><span data-stu-id="ff10a-132">Cross-origin connections</span></span>

<span data-ttu-id="ff10a-133">通常、ブラウザーは、要求されたページと同じドメインからの接続を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="ff10a-134">ただし、別のドメインへの接続が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-134">However, there are occasions when a connection to another domain is required.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ff10a-135">クライアントコードでは、相対 URL ではなく絶対 URL を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-135">The client code must use an absolute URL instead of a relative URL.</span></span> <span data-ttu-id="ff10a-136">`.withUrl("/chathub")` を `.withUrl("https://myappurl/chathub")` に変更します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-136">Change `.withUrl("/chathub")` to `.withUrl("https://myappurl/chathub")`.</span></span>

<span data-ttu-id="ff10a-137">悪意のあるサイトが別のサイトから機密データを読み取れないようにするため、既定では、 [クロスオリジン接続](xref:security/cors) は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="ff10a-137">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="ff10a-138">クロスオリジン要求を許可するには、クラスで有効にし `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-138">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="ff10a-139">クライアントからのハブメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="ff10a-139">Call hub methods from the client</span></span>

<span data-ttu-id="ff10a-140">JavaScript クライアントは、 [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection)の[invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---)メソッドを使用して、ハブでパブリックメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-140">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="ff10a-141">`invoke`メソッドは次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-141">The `invoke` method accepts:</span></span>

* <span data-ttu-id="ff10a-142">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="ff10a-142">The name of the hub method.</span></span>
* <span data-ttu-id="ff10a-143">ハブメソッドで定義されているすべての引数。</span><span class="sxs-lookup"><span data-stu-id="ff10a-143">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="ff10a-144">次の例では、ハブのメソッド名は `SendMessage` です。</span><span class="sxs-lookup"><span data-stu-id="ff10a-144">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="ff10a-145">`invoke`ハブメソッドの `user` 引数および引数にマップするために渡される2番目と3番目の引数 `message` 。</span><span class="sxs-lookup"><span data-stu-id="ff10a-145">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="ff10a-146">クライアントからのハブメソッドの呼び出しは、Azure SignalR サービスを *既定* のモードで使用している場合にのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-146">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="ff10a-147">詳細については、「 [よく寄せられる質問 (azure Signalr GitHub リポジトリ)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10a-147">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="ff10a-148">メソッドは、 `invoke` JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-148">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="ff10a-149">は、 `Promise` サーバーのメソッドがを返すときに戻り値 (存在する場合) を使用して解決されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-149">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="ff10a-150">サーバー上のメソッドがエラーをスローした場合、は `Promise` エラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-150">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="ff10a-151">`async` `await` `Promise` `then` `catch` これらのケースを処理するには、およびまたはのメソッドとメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-151">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="ff10a-152">JavaScript クライアントは、の [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) メソッドを使用して、ハブでパブリックメソッドを呼び出すこともでき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-152">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="ff10a-153">メソッドとは異なり `invoke` 、 `send` メソッドはサーバーからの応答を待機しません。</span><span class="sxs-lookup"><span data-stu-id="ff10a-153">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="ff10a-154">メソッドは、 `send` JavaScript を返し `Promise` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-154">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="ff10a-155">は、 `Promise` メッセージがサーバーに送信されたときに解決されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-155">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="ff10a-156">メッセージの送信中にエラーが発生した場合、は `Promise` エラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-156">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="ff10a-157">`async` `await` `Promise` `then` `catch` これらのケースを処理するには、およびまたはのメソッドとメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-157">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="ff10a-158">を使用する `send` と、サーバーがメッセージを受信するまで待機しません。</span><span class="sxs-lookup"><span data-stu-id="ff10a-158">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="ff10a-159">このため、サーバーからデータまたはエラーを返すことはできません。</span><span class="sxs-lookup"><span data-stu-id="ff10a-159">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="ff10a-160">ハブからクライアントメソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="ff10a-160">Call client methods from the hub</span></span>

<span data-ttu-id="ff10a-161">ハブからメッセージを受信するには、の [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) メソッドを使用してメソッドを定義し `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-161">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="ff10a-162">JavaScript クライアントメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="ff10a-162">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="ff10a-163">ハブがメソッドに渡す引数。</span><span class="sxs-lookup"><span data-stu-id="ff10a-163">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="ff10a-164">次の例では、メソッド名は `ReceiveMessage` です。</span><span class="sxs-lookup"><span data-stu-id="ff10a-164">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="ff10a-165">引数の名前は `user` と `message` です。</span><span class="sxs-lookup"><span data-stu-id="ff10a-165">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="ff10a-166">の前のコードは、 `connection.on` サーバー側のコードがメソッドを使用して呼び出すときに実行され <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-166">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="ff10a-167">SignalR とで定義されたメソッド名と引数を照合することによって、どのクライアントメソッドを呼び出すかを決定し `SendAsync` `connection.on` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-167">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="ff10a-168">ベストプラクティスとして、の後にの [start](/javascript/api/%40aspnet/signalr/hubconnection#start) メソッドを呼び出し `HubConnection` `on` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-168">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="ff10a-169">これにより、メッセージを受信する前にハンドラーが確実に登録されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-169">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="ff10a-170">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="ff10a-170">Error handling and logging</span></span>

<span data-ttu-id="ff10a-171">と `try` を `catch` `async` and またはのメソッドと共に使用して、 `await` `Promise` `catch` クライアント側のエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-171">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="ff10a-172">を使用し `console.error` て、ブラウザーのコンソールにエラーを出力します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-172">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="ff10a-173">接続が確立されたときにログに記録する logger と種類のイベントを渡すことによって、クライアント側のログトレースを設定します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-173">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="ff10a-174">メッセージは、指定されたログレベル以上でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-174">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="ff10a-175">使用可能なログレベルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ff10a-175">Available log levels are as follows:</span></span>

* <span data-ttu-id="ff10a-176">`signalR.LogLevel.Error`: エラーメッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-176">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="ff10a-177">`Error`メッセージのみをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-177">Logs `Error` messages only.</span></span>
* <span data-ttu-id="ff10a-178">`signalR.LogLevel.Warning`: 潜在的なエラーに関する警告メッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-178">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="ff10a-179">ログ `Warning` と `Error` メッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-179">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="ff10a-180">`signalR.LogLevel.Information`: エラーのないステータスメッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-180">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="ff10a-181">、、およびの各メッセージをログ `Information` に記録 `Warning` `Error` します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-181">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="ff10a-182">`signalR.LogLevel.Trace`: トレースメッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-182">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="ff10a-183">ハブとクライアント間で転送されたデータを含む、すべてをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-183">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="ff10a-184">ログレベルを構成するには、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)で[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-184">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="ff10a-185">メッセージは、ブラウザーコンソールにログ記録されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-185">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="ff10a-186">クライアントの再接続</span><span class="sxs-lookup"><span data-stu-id="ff10a-186">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="ff10a-187">自動的に再接続する</span><span class="sxs-lookup"><span data-stu-id="ff10a-187">Automatically reconnect</span></span>

<span data-ttu-id="ff10a-188">の JavaScript クライアントは、 SignalR HubConnectionBuilder のメソッドを使用して自動的に再接続するように構成でき `withAutomaticReconnect` ます。 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="ff10a-188">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="ff10a-189">既定では、自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="ff10a-189">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="ff10a-190">パラメーターを指定しない場合、は、 `withAutomaticReconnect()` 再接続を試行する前に、それぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-190">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="ff10a-191">再接続の試行を開始する前に、は状態に遷移し、 `HubConnection` コールバックを起動します。この状態に遷移し、 `HubConnectionState.Reconnecting` `onreconnecting` `Disconnected` `onclose` 自動再接続が構成されていないなどのコールバックをトリガーし `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-191">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="ff10a-192">これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-192">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="ff10a-193">最初の4回の試行でクライアントが正常に再接続した場合、は `HubConnection` 状態に戻り、 `Connected` コールバックを起動し `onreconnected` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-193">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="ff10a-194">これにより、接続が再確立されたことをユーザーに通知することができます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-194">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="ff10a-195">接続はサーバーにまったく新しいものであるため、 `connectionId` コールバックに新しいが提供され `onreconnected` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-195">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="ff10a-196">が `onreconnected` `connectionId` `HubConnection` [ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)するように構成されている場合、コールバックのパラメーターは未定義になります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-196">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="ff10a-197">`withAutomaticReconnect()` 最初の開始エラーを再試行するようにを構成しません `HubConnection` 。そのため、開始エラーは手動で処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-197">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="ff10a-198">最初の4回の試行でクライアントが正常に再接続されない場合、は `HubConnection` 状態に遷移し、 `Disconnected` その [閉じる](/javascript/api/%40aspnet/signalr/hubconnection#onclose) コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-198">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="ff10a-199">これにより、接続が完全に失われたことをユーザーに通知し、ページを最新の情報に更新することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ff10a-199">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="ff10a-200">再接続のタイミングを切断または変更する前に、カスタムの再接続試行回数を構成するために、では、 `withAutomaticReconnect` 各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-200">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="ff10a-201">前の例では、 `HubConnection` 接続が失われた直後に再接続を開始するようにを構成しています。</span><span class="sxs-lookup"><span data-stu-id="ff10a-201">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="ff10a-202">これは、既定の構成にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-202">This is also true for the default configuration.</span></span>

<span data-ttu-id="ff10a-203">最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-203">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="ff10a-204">2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-204">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="ff10a-205">このカスタム動作は、既定の構成のように、別の30秒でもう一度再接続を試行するのではなく、3回目の再接続試行の失敗後に停止することで、既定の動作から再び逸脱します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-205">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="ff10a-206">タイミングと自動再接続試行回数をさらに細かく制御する場合は、 `withAutomaticReconnect` インターフェイスを実装するオブジェクトを受け取り `IRetryPolicy` ます。このインターフェイスには、という名前の1つのメソッドがあり `nextRetryDelayInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-206">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="ff10a-207">`nextRetryDelayInMilliseconds` 型の1つの引数を受け取り `RetryContext` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-207">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="ff10a-208">には、、、およびの `RetryContext` 3 つのプロパティがあります。 `previousRetryCount` `elapsedMilliseconds` `retryReason` `number` `number` `Error`</span><span class="sxs-lookup"><span data-stu-id="ff10a-208">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="ff10a-209">最初の再接続を試行する前に、 `previousRetryCount` と `elapsedMilliseconds` の両方がゼロになり、は接続が失われる `retryReason` 原因となったエラーになります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-209">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="ff10a-210">再試行に失敗するたびに、 `previousRetryCount` が1ずつインクリメントされ `elapsedMilliseconds` ます。これまでの再接続にかかった時間 (ミリ秒単位) を反映して更新され `retryReason` ます。このエラーは、最後の再接続の試行が失敗した原因となったエラーになります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-210">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="ff10a-211">`nextRetryDelayInMilliseconds` は、次回の再接続が試行される前に待機するミリ秒数を表す数値を返すか、再接続を停止する必要がある場合はを返し `null` `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-211">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
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

<span data-ttu-id="ff10a-212">または、 [手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-212">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="ff10a-213">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="ff10a-213">Manually reconnect</span></span>

<span data-ttu-id="ff10a-214">次のコードは、標準的な手動再接続アプローチを示しています。</span><span class="sxs-lookup"><span data-stu-id="ff10a-214">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="ff10a-215">接続を開始する関数 (この場合は `start` 関数) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-215">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="ff10a-216">`start`接続のイベントハンドラーで関数を呼び出し `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-216">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="ff10a-217">実際の実装では、指数バックオフを使用するか、指定された回数の再試行を行います。</span><span class="sxs-lookup"><span data-stu-id="ff10a-217">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff10a-218">その他の資料</span><span class="sxs-lookup"><span data-stu-id="ff10a-218">Additional resources</span></span>

* [<span data-ttu-id="ff10a-219">JavaScript API リファレンス</span><span class="sxs-lookup"><span data-stu-id="ff10a-219">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="ff10a-220">JavaScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="ff10a-220">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ff10a-221">WebPack と TypeScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="ff10a-221">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="ff10a-222">ハブ</span><span class="sxs-lookup"><span data-stu-id="ff10a-222">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ff10a-223">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="ff10a-223">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ff10a-224">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="ff10a-224">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="ff10a-225">クロスオリジン要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="ff10a-225">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="ff10a-226">Azure SignalR サービスのサーバーレスドキュメント</span><span class="sxs-lookup"><span data-stu-id="ff10a-226">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [<span data-ttu-id="ff10a-227">接続エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="ff10a-227">Troubleshoot connection errors</span></span>](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff10a-228">作成者: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="ff10a-228">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="ff10a-229">ASP.NET Core SignalR JavaScript クライアントライブラリを使用すると、開発者はサーバー側のハブコードを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-229">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="ff10a-230">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ff10a-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="ff10a-231">クライアントパッケージをインストールする SignalR</span><span class="sxs-lookup"><span data-stu-id="ff10a-231">Install the SignalR client package</span></span>

<span data-ttu-id="ff10a-232">SignalRJavaScript クライアントライブラリは[npm](https://www.npmjs.com/)パッケージとして配信されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-232">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="ff10a-233">次のセクションでは、さまざまな方法でクライアントライブラリをインストールする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-233">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="ff10a-234">Npm を使用してインストールする</span><span class="sxs-lookup"><span data-stu-id="ff10a-234">Install with npm</span></span>

<span data-ttu-id="ff10a-235">Visual Studio を使用している場合は、ルートフォルダーで、 **パッケージマネージャーコンソール** から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-235">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="ff10a-236">Visual Studio Code には、 **統合ターミナル** から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-236">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="ff10a-237">npm は、 *node_modules \\ @aspnet\signalr\dist\browser* フォルダーにパッケージの内容をインストールします。</span><span class="sxs-lookup"><span data-stu-id="ff10a-237">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="ff10a-238">*Wwwroot \\ lib* フォルダーの下に *signalr* という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-238">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="ff10a-239">*signalr.js* ファイルを *wwwroot\lib\signalr* フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="ff10a-239">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="ff10a-240">SignalR要素で JavaScript クライアントを参照し `<script>` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-240">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="ff10a-241">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-241">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="ff10a-242">Content Delivery Network を使用する (CDN)</span><span class="sxs-lookup"><span data-stu-id="ff10a-242">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="ff10a-243">Npm の前提条件なしでクライアントライブラリを使用するには、CDN でホストされるクライアントライブラリのコピーを参照します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-243">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="ff10a-244">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-244">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="ff10a-245">クライアントライブラリは、次の CDNs で利用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-245">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="ff10a-246">cdnjs</span><span class="sxs-lookup"><span data-stu-id="ff10a-246">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="ff10a-247">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="ff10a-247">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="ff10a-248">unpkg</span><span class="sxs-lookup"><span data-stu-id="ff10a-248">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="ff10a-249">LibMan でのインストール</span><span class="sxs-lookup"><span data-stu-id="ff10a-249">Install with LibMan</span></span>

<span data-ttu-id="ff10a-250">[Libman](xref:client-side/libman/index) を使用すると、CDN でホストされているクライアントライブラリから特定のクライアントライブラリファイルをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-250">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="ff10a-251">たとえば、縮小版の JavaScript ファイルのみをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-251">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="ff10a-252">その方法の詳細については、「 [ SignalR クライアントライブラリの追加](xref:tutorials/signalr#add-the-signalr-client-library)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10a-252">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="ff10a-253">ハブへの接続</span><span class="sxs-lookup"><span data-stu-id="ff10a-253">Connect to a hub</span></span>

<span data-ttu-id="ff10a-254">次のコードでは、接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-254">The following code creates and starts a connection.</span></span> <span data-ttu-id="ff10a-255">ハブの名前は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="ff10a-255">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="ff10a-256">クロスオリジン接続</span><span class="sxs-lookup"><span data-stu-id="ff10a-256">Cross-origin connections</span></span>

<span data-ttu-id="ff10a-257">通常、ブラウザーは、要求されたページと同じドメインからの接続を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-257">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="ff10a-258">ただし、別のドメインへの接続が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-258">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="ff10a-259">悪意のあるサイトが別のサイトから機密データを読み取れないようにするため、既定では、 [クロスオリジン接続](xref:security/cors) は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="ff10a-259">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="ff10a-260">クロスオリジン要求を許可するには、クラスで有効にし `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-260">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="ff10a-261">クライアントからのハブメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="ff10a-261">Call hub methods from client</span></span>

<span data-ttu-id="ff10a-262">JavaScript クライアントは、 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)の[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)メソッドを使用して、ハブでパブリックメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-262">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="ff10a-263">メソッドは、 `invoke` 次の2つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-263">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="ff10a-264">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="ff10a-264">The name of the hub method.</span></span> <span data-ttu-id="ff10a-265">次の例では、ハブのメソッド名は `SendMessage` です。</span><span class="sxs-lookup"><span data-stu-id="ff10a-265">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="ff10a-266">ハブメソッドで定義されているすべての引数。</span><span class="sxs-lookup"><span data-stu-id="ff10a-266">Any arguments defined in the hub method.</span></span> <span data-ttu-id="ff10a-267">次の例では、引数名は `message` です。</span><span class="sxs-lookup"><span data-stu-id="ff10a-267">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="ff10a-268">このコード例では、Internet Explorer を除くすべての主要なブラウザーの現在のバージョンでサポートされている、矢印関数の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-268">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="ff10a-269">クライアントからのハブメソッドの呼び出しは、Azure SignalR サービスを *既定* のモードで使用している場合にのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-269">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="ff10a-270">詳細については、「 [よく寄せられる質問 (azure Signalr GitHub リポジトリ)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10a-270">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="ff10a-271">メソッドは、 `invoke` JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-271">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="ff10a-272">は、 `Promise` サーバーのメソッドがを返すときに戻り値 (存在する場合) を使用して解決されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-272">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="ff10a-273">サーバー上のメソッドがエラーをスローした場合、は `Promise` エラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-273">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="ff10a-274">これらの `then` `catch` `Promise` ケース (または構文) を処理するには、自身でメソッドとメソッドを使用し `await` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-274">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="ff10a-275">メソッドは、 `send` JavaScript を返し `Promise` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-275">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="ff10a-276">は、 `Promise` メッセージがサーバーに送信されたときに解決されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-276">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="ff10a-277">メッセージの送信中にエラーが発生した場合、は `Promise` エラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-277">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="ff10a-278">これらの `then` `catch` `Promise` ケース (または構文) を処理するには、自身でメソッドとメソッドを使用し `await` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-278">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="ff10a-279">を使用する `send` と、サーバーがメッセージを受信するまで待機しません。</span><span class="sxs-lookup"><span data-stu-id="ff10a-279">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="ff10a-280">このため、サーバーからデータまたはエラーを返すことはできません。</span><span class="sxs-lookup"><span data-stu-id="ff10a-280">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="ff10a-281">ハブからクライアントメソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="ff10a-281">Call client methods from hub</span></span>

<span data-ttu-id="ff10a-282">ハブからメッセージを受信するには、の [on](/javascript/api/%40aspnet/signalr/hubconnection#on) メソッドを使用してメソッドを定義し `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-282">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="ff10a-283">JavaScript クライアントメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="ff10a-283">The name of the JavaScript client method.</span></span> <span data-ttu-id="ff10a-284">次の例では、メソッド名は `ReceiveMessage` です。</span><span class="sxs-lookup"><span data-stu-id="ff10a-284">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="ff10a-285">ハブがメソッドに渡す引数。</span><span class="sxs-lookup"><span data-stu-id="ff10a-285">Arguments the hub passes to the method.</span></span> <span data-ttu-id="ff10a-286">次の例では、引数の値は `message` です。</span><span class="sxs-lookup"><span data-stu-id="ff10a-286">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="ff10a-287">の前のコードは、 `connection.on` サーバー側のコードがメソッドを使用して呼び出したときに実行され <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-287">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="ff10a-288">SignalR とで定義されたメソッド名と引数を照合することによって、どのクライアントメソッドを呼び出すかを決定し `SendAsync` `connection.on` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-288">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="ff10a-289">ベストプラクティスとして、の後にの [start](/javascript/api/%40aspnet/signalr/hubconnection#start) メソッドを呼び出し `HubConnection` `on` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-289">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="ff10a-290">これにより、メッセージを受信する前にハンドラーが確実に登録されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-290">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="ff10a-291">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="ff10a-291">Error handling and logging</span></span>

<span data-ttu-id="ff10a-292">メソッドを `catch` メソッドの最後にチェーンして、 `start` クライアント側のエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-292">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="ff10a-293">`console.error`ブラウザーのコンソールにエラーを出力するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-293">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="ff10a-294">接続が確立されたときにログに記録する logger と種類のイベントを渡すことによって、クライアント側のログトレースを設定します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-294">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="ff10a-295">メッセージは、指定されたログレベル以上でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-295">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="ff10a-296">使用可能なログレベルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ff10a-296">Available log levels are as follows:</span></span>

* <span data-ttu-id="ff10a-297">`signalR.LogLevel.Error`: エラーメッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-297">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="ff10a-298">`Error`メッセージのみをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-298">Logs `Error` messages only.</span></span>
* <span data-ttu-id="ff10a-299">`signalR.LogLevel.Warning`: 潜在的なエラーに関する警告メッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-299">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="ff10a-300">ログ `Warning` と `Error` メッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-300">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="ff10a-301">`signalR.LogLevel.Information`: エラーのないステータスメッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-301">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="ff10a-302">、、およびの各メッセージをログ `Information` に記録 `Warning` `Error` します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-302">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="ff10a-303">`signalR.LogLevel.Trace`: トレースメッセージ。</span><span class="sxs-lookup"><span data-stu-id="ff10a-303">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="ff10a-304">ハブとクライアント間で転送されたデータを含む、すべてをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-304">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="ff10a-305">ログレベルを構成するには、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)で[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ff10a-305">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="ff10a-306">メッセージは、ブラウザーコンソールに記録されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-306">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="ff10a-307">クライアントの再接続</span><span class="sxs-lookup"><span data-stu-id="ff10a-307">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="ff10a-308">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="ff10a-308">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="ff10a-309">3.0 より前では、の JavaScript クライアントは SignalR 自動的に再接続しません。</span><span class="sxs-lookup"><span data-stu-id="ff10a-309">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="ff10a-310">クライアントを手動で再接続するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10a-310">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="ff10a-311">次のコードは、標準的な手動再接続アプローチを示しています。</span><span class="sxs-lookup"><span data-stu-id="ff10a-311">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="ff10a-312">接続を開始する関数 (この場合は `start` 関数) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-312">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="ff10a-313">`start`接続のイベントハンドラーで関数を呼び出し `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="ff10a-313">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="ff10a-314">実際の実装では、指数バックオフを使用するか、指定された回数の再試行を行います。</span><span class="sxs-lookup"><span data-stu-id="ff10a-314">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff10a-315">その他の資料</span><span class="sxs-lookup"><span data-stu-id="ff10a-315">Additional resources</span></span>

* [<span data-ttu-id="ff10a-316">JavaScript API リファレンス</span><span class="sxs-lookup"><span data-stu-id="ff10a-316">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="ff10a-317">JavaScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="ff10a-317">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ff10a-318">WebPack と TypeScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="ff10a-318">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="ff10a-319">ハブ</span><span class="sxs-lookup"><span data-stu-id="ff10a-319">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ff10a-320">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="ff10a-320">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ff10a-321">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="ff10a-321">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="ff10a-322">クロスオリジン要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="ff10a-322">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="ff10a-323">Azure SignalR サービスのサーバーレスドキュメント</span><span class="sxs-lookup"><span data-stu-id="ff10a-323">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
