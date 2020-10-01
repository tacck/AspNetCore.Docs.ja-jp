---
title: ASP.NET Core SignalR JavaScript クライアント
author: bradygaster
description: ASP.NET Core JavaScript クライアントの概要について説明 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: 6fc586d144547585ef75d653bf54193def5c8b7f
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606683"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="5551e-103">ASP.NET Core SignalR JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="5551e-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5551e-104">作成者: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="5551e-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="5551e-105">ASP.NET Core SignalR JavaScript クライアントライブラリを使用すると、開発者はサーバー側のハブコードを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="5551e-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="5551e-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5551e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="5551e-107">クライアントパッケージをインストールする SignalR</span><span class="sxs-lookup"><span data-stu-id="5551e-107">Install the SignalR client package</span></span>

<span data-ttu-id="5551e-108">SignalRJavaScript クライアントライブラリは[npm](https://www.npmjs.com/)パッケージとして配信されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="5551e-109">次のセクションでは、さまざまな方法でクライアントライブラリをインストールする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5551e-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="5551e-110">Npm を使用してインストールする</span><span class="sxs-lookup"><span data-stu-id="5551e-110">Install with npm</span></span>

<span data-ttu-id="5551e-111">Visual Studio の場合は、ルートフォルダーで、 **パッケージマネージャーコンソール** から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5551e-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="5551e-112">Visual Studio Code には、 **統合ターミナル**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5551e-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="5551e-113">npm は、 \*node_modules \\ @microsoft\signalr\dist\browser \*フォルダーにパッケージの内容をインストールします。</span><span class="sxs-lookup"><span data-stu-id="5551e-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="5551e-114">*Wwwroot \\ lib*フォルダーの下に*signalr*という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="5551e-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="5551e-115">*signalr.js*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="5551e-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="5551e-116">SignalR要素で JavaScript クライアントを参照し `<script>` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="5551e-117">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5551e-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="5551e-118">Content Delivery Network を使用する (CDN)</span><span class="sxs-lookup"><span data-stu-id="5551e-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="5551e-119">Npm の前提条件なしでクライアントライブラリを使用するには、CDN でホストされるクライアントライブラリのコピーを参照します。</span><span class="sxs-lookup"><span data-stu-id="5551e-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="5551e-120">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5551e-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="5551e-121">クライアントライブラリは、次の CDNs で利用できます。</span><span class="sxs-lookup"><span data-stu-id="5551e-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="5551e-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="5551e-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="5551e-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="5551e-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="5551e-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="5551e-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="5551e-125">LibMan でのインストール</span><span class="sxs-lookup"><span data-stu-id="5551e-125">Install with LibMan</span></span>

<span data-ttu-id="5551e-126">[Libman](xref:client-side/libman/index) を使用すると、CDN でホストされているクライアントライブラリから特定のクライアントライブラリファイルをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="5551e-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="5551e-127">たとえば、縮小版の JavaScript ファイルのみをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="5551e-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="5551e-128">その方法の詳細については、「 [ SignalR クライアントライブラリの追加](xref:tutorials/signalr#add-the-signalr-client-library)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5551e-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="5551e-129">ハブへの接続</span><span class="sxs-lookup"><span data-stu-id="5551e-129">Connect to a hub</span></span>

<span data-ttu-id="5551e-130">次のコードでは、接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="5551e-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="5551e-131">ハブの名前では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="5551e-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="5551e-132">クロスオリジン接続</span><span class="sxs-lookup"><span data-stu-id="5551e-132">Cross-origin connections</span></span>

<span data-ttu-id="5551e-133">通常、ブラウザーは、要求されたページと同じドメインからの接続を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="5551e-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="5551e-134">ただし、別のドメインへの接続が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="5551e-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="5551e-135">悪意のあるサイトが別のサイトから機密データを読み取れないようにするため、既定では、 [クロスオリジン接続](xref:security/cors) は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="5551e-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="5551e-136">クロスオリジン要求を許可するには、クラスで有効にし `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="5551e-137">クライアントからのハブメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="5551e-137">Call hub methods from the client</span></span>

<span data-ttu-id="5551e-138">JavaScript クライアントは、 [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection)の[invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---)メソッドを使用して、ハブでパブリックメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5551e-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="5551e-139">`invoke`メソッドは次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="5551e-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="5551e-140">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="5551e-140">The name of the hub method.</span></span>
* <span data-ttu-id="5551e-141">ハブメソッドで定義されているすべての引数。</span><span class="sxs-lookup"><span data-stu-id="5551e-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="5551e-142">次の例では、ハブのメソッド名は `SendMessage` です。</span><span class="sxs-lookup"><span data-stu-id="5551e-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="5551e-143">`invoke`ハブメソッドの `user` 引数および引数にマップするために渡される2番目と3番目の引数 `message` 。</span><span class="sxs-lookup"><span data-stu-id="5551e-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="5551e-144">クライアントからのハブメソッドの呼び出しは、Azure SignalR サービスを *既定* のモードで使用している場合にのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="5551e-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="5551e-145">詳細については、「 [よく寄せられる質問 (azure Signalr GitHub リポジトリ)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5551e-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="5551e-146">メソッドは、 `invoke` JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。</span><span class="sxs-lookup"><span data-stu-id="5551e-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="5551e-147">は、 `Promise` サーバーのメソッドがを返すときに戻り値 (存在する場合) を使用して解決されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="5551e-148">サーバー上のメソッドがエラーをスローした場合、は `Promise` エラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="5551e-149">`async` `await` `Promise` `then` `catch` これらのケースを処理するには、およびまたはのメソッドとメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5551e-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="5551e-150">JavaScript クライアントは、の [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) メソッドを使用して、ハブでパブリックメソッドを呼び出すこともでき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="5551e-151">メソッドとは異なり `invoke` 、 `send` メソッドはサーバーからの応答を待機しません。</span><span class="sxs-lookup"><span data-stu-id="5551e-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="5551e-152">メソッドは、 `send` JavaScript を返し `Promise` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="5551e-153">は、 `Promise` メッセージがサーバーに送信されたときに解決されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="5551e-154">メッセージの送信中にエラーが発生した場合、は `Promise` エラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="5551e-155">`async` `await` `Promise` `then` `catch` これらのケースを処理するには、およびまたはのメソッドとメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5551e-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="5551e-156">を使用する `send` と、サーバーがメッセージを受信するまで待機しません。</span><span class="sxs-lookup"><span data-stu-id="5551e-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="5551e-157">このため、サーバーからデータまたはエラーを返すことはできません。</span><span class="sxs-lookup"><span data-stu-id="5551e-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="5551e-158">ハブからクライアントメソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="5551e-158">Call client methods from the hub</span></span>

<span data-ttu-id="5551e-159">ハブからメッセージを受信するには、の [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) メソッドを使用してメソッドを定義し `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="5551e-160">JavaScript クライアントメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="5551e-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="5551e-161">ハブがメソッドに渡す引数。</span><span class="sxs-lookup"><span data-stu-id="5551e-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="5551e-162">次の例では、メソッド名は `ReceiveMessage` です。</span><span class="sxs-lookup"><span data-stu-id="5551e-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="5551e-163">引数の名前は `user` と `message` です。</span><span class="sxs-lookup"><span data-stu-id="5551e-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="5551e-164">の前のコードは、 `connection.on` サーバー側のコードがメソッドを使用して呼び出すときに実行され <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="5551e-165">SignalR とで定義されたメソッド名と引数を照合することによって、どのクライアントメソッドを呼び出すかを決定し `SendAsync` `connection.on` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="5551e-166">ベストプラクティスとして、の後にの [start](/javascript/api/%40aspnet/signalr/hubconnection#start) メソッドを呼び出し `HubConnection` `on` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="5551e-167">これにより、メッセージを受信する前にハンドラーが確実に登録されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="5551e-168">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="5551e-168">Error handling and logging</span></span>

<span data-ttu-id="5551e-169">と `try` を `catch` `async` and またはのメソッドと共に使用して、 `await` `Promise` `catch` クライアント側のエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="5551e-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="5551e-170">を使用し `console.error` て、ブラウザーのコンソールにエラーを出力します。</span><span class="sxs-lookup"><span data-stu-id="5551e-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="5551e-171">接続が確立されたときにログに記録する logger と種類のイベントを渡すことによって、クライアント側のログトレースを設定します。</span><span class="sxs-lookup"><span data-stu-id="5551e-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="5551e-172">メッセージは、指定されたログレベル以上でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="5551e-173">使用可能なログレベルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5551e-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="5551e-174">`signalR.LogLevel.Error`: エラーメッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="5551e-175">`Error`メッセージのみをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="5551e-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="5551e-176">`signalR.LogLevel.Warning`: 潜在的なエラーに関する警告メッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="5551e-177">ログ `Warning` と `Error` メッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="5551e-178">`signalR.LogLevel.Information`: エラーのないステータスメッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="5551e-179">、、およびの各メッセージをログ `Information` に記録 `Warning` `Error` します。</span><span class="sxs-lookup"><span data-stu-id="5551e-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="5551e-180">`signalR.LogLevel.Trace`: トレースメッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="5551e-181">ハブとクライアント間で転送されたデータを含む、すべてをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="5551e-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="5551e-182">ログレベルを構成するには、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)で[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5551e-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="5551e-183">メッセージは、ブラウザーコンソールにログ記録されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="5551e-184">クライアントの再接続</span><span class="sxs-lookup"><span data-stu-id="5551e-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="5551e-185">自動的に再接続する</span><span class="sxs-lookup"><span data-stu-id="5551e-185">Automatically reconnect</span></span>

<span data-ttu-id="5551e-186">の JavaScript クライアントは、 SignalR HubConnectionBuilder のメソッドを使用して自動的に再接続するように構成でき `withAutomaticReconnect` ます。 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="5551e-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="5551e-187">既定では、自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="5551e-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="5551e-188">パラメーターを指定しない場合、は、 `withAutomaticReconnect()` 再接続を試行する前に、それぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。</span><span class="sxs-lookup"><span data-stu-id="5551e-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="5551e-189">再接続の試行を開始する前に、は状態に遷移し、 `HubConnection` コールバックを起動します。この状態に遷移し、 `HubConnectionState.Reconnecting` `onreconnecting` `Disconnected` `onclose` 自動再接続が構成されていないなどのコールバックをトリガーし `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="5551e-190">これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="5551e-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="5551e-191">最初の4回の試行でクライアントが正常に再接続した場合、は `HubConnection` 状態に戻り、 `Connected` コールバックを起動し `onreconnected` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="5551e-192">これにより、接続が再確立されたことをユーザーに通知することができます。</span><span class="sxs-lookup"><span data-stu-id="5551e-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="5551e-193">接続はサーバーにまったく新しいものであるため、 `connectionId` コールバックに新しいが提供され `onreconnected` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="5551e-194">が `onreconnected` `connectionId` `HubConnection` [ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)するように構成されている場合、コールバックのパラメーターは未定義になります。</span><span class="sxs-lookup"><span data-stu-id="5551e-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="5551e-195">`withAutomaticReconnect()` 最初の開始エラーを再試行するようにを構成しません `HubConnection` 。そのため、開始エラーは手動で処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5551e-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="5551e-196">最初の4回の試行でクライアントが正常に再接続されない場合、は `HubConnection` 状態に遷移し、 `Disconnected` その [閉じる](/javascript/api/%40aspnet/signalr/hubconnection#onclose) コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="5551e-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="5551e-197">これにより、接続が完全に失われたことをユーザーに通知し、ページを最新の情報に更新することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5551e-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="5551e-198">再接続のタイミングを切断または変更する前に、カスタムの再接続試行回数を構成するために、では、 `withAutomaticReconnect` 各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="5551e-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="5551e-199">前の例では、 `HubConnection` 接続が失われた直後に再接続を開始するようにを構成しています。</span><span class="sxs-lookup"><span data-stu-id="5551e-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="5551e-200">これは、既定の構成にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="5551e-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="5551e-201">最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="5551e-202">2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。</span><span class="sxs-lookup"><span data-stu-id="5551e-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="5551e-203">このカスタム動作は、既定の構成のように、別の30秒でもう一度再接続を試行するのではなく、3回目の再接続試行の失敗後に停止することで、既定の動作から再び逸脱します。</span><span class="sxs-lookup"><span data-stu-id="5551e-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="5551e-204">タイミングと自動再接続試行回数をさらに細かく制御する場合は、 `withAutomaticReconnect` インターフェイスを実装するオブジェクトを受け取り `IRetryPolicy` ます。このインターフェイスには、という名前の1つのメソッドがあり `nextRetryDelayInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="5551e-205">`nextRetryDelayInMilliseconds` 型の1つの引数を受け取り `RetryContext` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="5551e-206">には、、、およびの `RetryContext` 3 つのプロパティがあります。 `previousRetryCount` `elapsedMilliseconds` `retryReason` `number` `number` `Error`</span><span class="sxs-lookup"><span data-stu-id="5551e-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="5551e-207">最初の再接続を試行する前に、 `previousRetryCount` と `elapsedMilliseconds` の両方がゼロになり、は接続が失われる `retryReason` 原因となったエラーになります。</span><span class="sxs-lookup"><span data-stu-id="5551e-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="5551e-208">再試行に失敗するたびに、 `previousRetryCount` が1ずつインクリメントされ `elapsedMilliseconds` ます。これまでの再接続にかかった時間 (ミリ秒単位) を反映して更新され `retryReason` ます。このエラーは、最後の再接続の試行が失敗した原因となったエラーになります。</span><span class="sxs-lookup"><span data-stu-id="5551e-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="5551e-209">`nextRetryDelayInMilliseconds` は、次回の再接続が試行される前に待機するミリ秒数を表す数値を返すか、再接続を停止する必要がある場合はを返し `null` `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="5551e-210">または、 [手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="5551e-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="5551e-211">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="5551e-211">Manually reconnect</span></span>

<span data-ttu-id="5551e-212">次のコードは、標準的な手動再接続アプローチを示しています。</span><span class="sxs-lookup"><span data-stu-id="5551e-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="5551e-213">接続を開始する関数 (この場合は `start` 関数) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="5551e-214">`start`接続のイベントハンドラーで関数を呼び出し `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="5551e-215">実際の実装では、指数バックオフを使用するか、指定された回数の再試行を行います。</span><span class="sxs-lookup"><span data-stu-id="5551e-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="troubleshoot-websocket-handshake-errors"></a><span data-ttu-id="5551e-216">WebSocket ハンドシェイクエラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="5551e-216">Troubleshoot WebSocket handshake errors</span></span>

<span data-ttu-id="5551e-217">このセクションでは、ASP.NET Core hub への接続を確立しようとしたときに発生する *"WebSocket ハンドシェイク中のエラー"* 例外について説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-217">This section provides help on the *"Error during WebSocket handshake"* exception that occurs when trying to establish connection to ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="5551e-218">応答コード400または503</span><span class="sxs-lookup"><span data-stu-id="5551e-218">Response code 400 or 503</span></span>

<span data-ttu-id="5551e-219">次のエラーが発生した場合:</span><span class="sxs-lookup"><span data-stu-id="5551e-219">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="5551e-220">このエラーは、通常、クライアントのみが Websocket トランスポートを使用するために発生しますが、Websocket プロトコルはサーバーで有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="5551e-220">The error is usually caused by client only use WebSockets transport but WebSockets protocol is not enabled on server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="5551e-221">応答コード307</span><span class="sxs-lookup"><span data-stu-id="5551e-221">Response code 307</span></span>

```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="5551e-222">多くの場合、 SignalR ハブサーバーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5551e-222">Frequently this occurs when the SignalR hub server:</span></span>

* <span data-ttu-id="5551e-223">HTTP と HTTPS の両方をリッスンして応答します。</span><span class="sxs-lookup"><span data-stu-id="5551e-223">Listens to and responds over both HTTP and HTTPS.</span></span>
* <span data-ttu-id="5551e-224">は、でを呼び出して HTTPS を強制するように構成されている `UseHttpsRedirection` `Startup` か、URL 書き換えルールを使用して https を強制します。</span><span class="sxs-lookup"><span data-stu-id="5551e-224">Is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="5551e-225">このエラーは、を使用してクライアント側で HTTP URL を指定することによって発生することがあり `.withUrl("http://xxx/HubName")` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-225">This error can be caused by specifying the HTTP URL on client side using `.withUrl("http://xxx/HubName")`.</span></span> <span data-ttu-id="5551e-226">この場合の修正は、HTTPS URL を使用するようにコードを変更することです。</span><span class="sxs-lookup"><span data-stu-id="5551e-226">The fix for this case is modifying the code to use an HTTPS URL.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="5551e-227">応答コード404</span><span class="sxs-lookup"><span data-stu-id="5551e-227">Response code 404</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

<span data-ttu-id="5551e-228">アプリが localhost で動作するが、IIS サーバーに発行した後にこのエラーが返される場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5551e-228">If the app works on localhost, but returns this error after publishing to IIS server:</span></span>

* <span data-ttu-id="5551e-229">ASP.NET Core SignalR アプリが IIS サブアプリケーションとしてホストされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5551e-229">Verify the ASP.NET Core SignalR app is hosted as an IIS sub-application.</span></span>
* <span data-ttu-id="5551e-230">JavaScript クライアント側では、サブアプリの pathbase を使用して URL を設定しないで SignalR `.withUrl("/SubAppName/HubName")` ください。</span><span class="sxs-lookup"><span data-stu-id="5551e-230">Don't set URL with the sub-app's pathbase on SignalR JavaScript client side `.withUrl("/SubAppName/HubName")`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5551e-231">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="5551e-231">Additional resources</span></span>

* [<span data-ttu-id="5551e-232">JavaScript API リファレンス</span><span class="sxs-lookup"><span data-stu-id="5551e-232">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="5551e-233">JavaScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="5551e-233">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5551e-234">WebPack と TypeScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="5551e-234">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="5551e-235">ハブ</span><span class="sxs-lookup"><span data-stu-id="5551e-235">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="5551e-236">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="5551e-236">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="5551e-237">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="5551e-237">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="5551e-238">クロスオリジン要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="5551e-238">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="5551e-239">Azure SignalR サービスのサーバーレスドキュメント</span><span class="sxs-lookup"><span data-stu-id="5551e-239">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5551e-240">作成者: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="5551e-240">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="5551e-241">ASP.NET Core SignalR JavaScript クライアントライブラリを使用すると、開発者はサーバー側のハブコードを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="5551e-241">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="5551e-242">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5551e-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="5551e-243">クライアントパッケージをインストールする SignalR</span><span class="sxs-lookup"><span data-stu-id="5551e-243">Install the SignalR client package</span></span>

<span data-ttu-id="5551e-244">SignalRJavaScript クライアントライブラリは[npm](https://www.npmjs.com/)パッケージとして配信されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-244">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="5551e-245">次のセクションでは、さまざまな方法でクライアントライブラリをインストールする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5551e-245">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="5551e-246">Npm を使用してインストールする</span><span class="sxs-lookup"><span data-stu-id="5551e-246">Install with npm</span></span>

<span data-ttu-id="5551e-247">Visual Studio を使用している場合は、ルートフォルダーで、 **パッケージマネージャーコンソール** から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5551e-247">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="5551e-248">Visual Studio Code には、 **統合ターミナル**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5551e-248">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="5551e-249">npm は、 \*node_modules \\ @aspnet\signalr\dist\browser \*フォルダーにパッケージの内容をインストールします。</span><span class="sxs-lookup"><span data-stu-id="5551e-249">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="5551e-250">*Wwwroot \\ lib*フォルダーの下に*signalr*という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="5551e-250">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="5551e-251">*signalr.js*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="5551e-251">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="5551e-252">SignalR要素で JavaScript クライアントを参照し `<script>` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-252">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="5551e-253">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5551e-253">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="5551e-254">Content Delivery Network を使用する (CDN)</span><span class="sxs-lookup"><span data-stu-id="5551e-254">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="5551e-255">Npm の前提条件なしでクライアントライブラリを使用するには、CDN でホストされるクライアントライブラリのコピーを参照します。</span><span class="sxs-lookup"><span data-stu-id="5551e-255">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="5551e-256">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5551e-256">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="5551e-257">クライアントライブラリは、次の CDNs で利用できます。</span><span class="sxs-lookup"><span data-stu-id="5551e-257">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="5551e-258">cdnjs</span><span class="sxs-lookup"><span data-stu-id="5551e-258">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="5551e-259">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="5551e-259">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="5551e-260">unpkg</span><span class="sxs-lookup"><span data-stu-id="5551e-260">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="5551e-261">LibMan でのインストール</span><span class="sxs-lookup"><span data-stu-id="5551e-261">Install with LibMan</span></span>

<span data-ttu-id="5551e-262">[Libman](xref:client-side/libman/index) を使用すると、CDN でホストされているクライアントライブラリから特定のクライアントライブラリファイルをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="5551e-262">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="5551e-263">たとえば、縮小版の JavaScript ファイルのみをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="5551e-263">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="5551e-264">その方法の詳細については、「 [ SignalR クライアントライブラリの追加](xref:tutorials/signalr#add-the-signalr-client-library)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5551e-264">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="5551e-265">ハブへの接続</span><span class="sxs-lookup"><span data-stu-id="5551e-265">Connect to a hub</span></span>

<span data-ttu-id="5551e-266">次のコードでは、接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="5551e-266">The following code creates and starts a connection.</span></span> <span data-ttu-id="5551e-267">ハブの名前は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="5551e-267">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="5551e-268">クロスオリジン接続</span><span class="sxs-lookup"><span data-stu-id="5551e-268">Cross-origin connections</span></span>

<span data-ttu-id="5551e-269">通常、ブラウザーは、要求されたページと同じドメインからの接続を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="5551e-269">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="5551e-270">ただし、別のドメインへの接続が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="5551e-270">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="5551e-271">悪意のあるサイトが別のサイトから機密データを読み取れないようにするため、既定では、 [クロスオリジン接続](xref:security/cors) は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="5551e-271">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="5551e-272">クロスオリジン要求を許可するには、クラスで有効にし `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-272">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="5551e-273">クライアントからのハブメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="5551e-273">Call hub methods from client</span></span>

<span data-ttu-id="5551e-274">JavaScript クライアントは、 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)の[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)メソッドを使用して、ハブでパブリックメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5551e-274">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="5551e-275">メソッドは、 `invoke` 次の2つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="5551e-275">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="5551e-276">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="5551e-276">The name of the hub method.</span></span> <span data-ttu-id="5551e-277">次の例では、ハブのメソッド名は `SendMessage` です。</span><span class="sxs-lookup"><span data-stu-id="5551e-277">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="5551e-278">ハブメソッドで定義されているすべての引数。</span><span class="sxs-lookup"><span data-stu-id="5551e-278">Any arguments defined in the hub method.</span></span> <span data-ttu-id="5551e-279">次の例では、引数名は `message` です。</span><span class="sxs-lookup"><span data-stu-id="5551e-279">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="5551e-280">このコード例では、Internet Explorer を除くすべての主要なブラウザーの現在のバージョンでサポートされている、矢印関数の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="5551e-280">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="5551e-281">クライアントからのハブメソッドの呼び出しは、Azure SignalR サービスを *既定* のモードで使用している場合にのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="5551e-281">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="5551e-282">詳細については、「 [よく寄せられる質問 (azure Signalr GitHub リポジトリ)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5551e-282">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="5551e-283">メソッドは、 `invoke` JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。</span><span class="sxs-lookup"><span data-stu-id="5551e-283">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="5551e-284">は、 `Promise` サーバーのメソッドがを返すときに戻り値 (存在する場合) を使用して解決されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-284">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="5551e-285">サーバー上のメソッドがエラーをスローした場合、は `Promise` エラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-285">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="5551e-286">これらの `then` `catch` `Promise` ケース (または構文) を処理するには、自身でメソッドとメソッドを使用し `await` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-286">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="5551e-287">メソッドは、 `send` JavaScript を返し `Promise` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-287">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="5551e-288">は、 `Promise` メッセージがサーバーに送信されたときに解決されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-288">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="5551e-289">メッセージの送信中にエラーが発生した場合、は `Promise` エラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-289">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="5551e-290">これらの `then` `catch` `Promise` ケース (または構文) を処理するには、自身でメソッドとメソッドを使用し `await` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-290">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="5551e-291">を使用する `send` と、サーバーがメッセージを受信するまで待機しません。</span><span class="sxs-lookup"><span data-stu-id="5551e-291">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="5551e-292">このため、サーバーからデータまたはエラーを返すことはできません。</span><span class="sxs-lookup"><span data-stu-id="5551e-292">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="5551e-293">ハブからクライアントメソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="5551e-293">Call client methods from hub</span></span>

<span data-ttu-id="5551e-294">ハブからメッセージを受信するには、の [on](/javascript/api/%40aspnet/signalr/hubconnection#on) メソッドを使用してメソッドを定義し `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-294">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="5551e-295">JavaScript クライアントメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="5551e-295">The name of the JavaScript client method.</span></span> <span data-ttu-id="5551e-296">次の例では、メソッド名は `ReceiveMessage` です。</span><span class="sxs-lookup"><span data-stu-id="5551e-296">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="5551e-297">ハブがメソッドに渡す引数。</span><span class="sxs-lookup"><span data-stu-id="5551e-297">Arguments the hub passes to the method.</span></span> <span data-ttu-id="5551e-298">次の例では、引数の値は `message` です。</span><span class="sxs-lookup"><span data-stu-id="5551e-298">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="5551e-299">の前のコードは、 `connection.on` サーバー側のコードがメソッドを使用して呼び出したときに実行され <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-299">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="5551e-300">SignalR とで定義されたメソッド名と引数を照合することによって、どのクライアントメソッドを呼び出すかを決定し `SendAsync` `connection.on` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-300">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="5551e-301">ベストプラクティスとして、の後にの [start](/javascript/api/%40aspnet/signalr/hubconnection#start) メソッドを呼び出し `HubConnection` `on` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-301">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="5551e-302">これにより、メッセージを受信する前にハンドラーが確実に登録されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-302">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="5551e-303">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="5551e-303">Error handling and logging</span></span>

<span data-ttu-id="5551e-304">メソッドを `catch` メソッドの最後にチェーンして、 `start` クライアント側のエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="5551e-304">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="5551e-305">`console.error`ブラウザーのコンソールにエラーを出力するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="5551e-305">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="5551e-306">接続が確立されたときにログに記録する logger と種類のイベントを渡すことによって、クライアント側のログトレースを設定します。</span><span class="sxs-lookup"><span data-stu-id="5551e-306">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="5551e-307">メッセージは、指定されたログレベル以上でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-307">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="5551e-308">使用可能なログレベルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5551e-308">Available log levels are as follows:</span></span>

* <span data-ttu-id="5551e-309">`signalR.LogLevel.Error`: エラーメッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-309">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="5551e-310">`Error`メッセージのみをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="5551e-310">Logs `Error` messages only.</span></span>
* <span data-ttu-id="5551e-311">`signalR.LogLevel.Warning`: 潜在的なエラーに関する警告メッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-311">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="5551e-312">ログ `Warning` と `Error` メッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-312">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="5551e-313">`signalR.LogLevel.Information`: エラーのないステータスメッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-313">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="5551e-314">、、およびの各メッセージをログ `Information` に記録 `Warning` `Error` します。</span><span class="sxs-lookup"><span data-stu-id="5551e-314">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="5551e-315">`signalR.LogLevel.Trace`: トレースメッセージ。</span><span class="sxs-lookup"><span data-stu-id="5551e-315">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="5551e-316">ハブとクライアント間で転送されたデータを含む、すべてをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="5551e-316">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="5551e-317">ログレベルを構成するには、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)で[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5551e-317">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="5551e-318">メッセージは、ブラウザーコンソールに記録されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-318">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="5551e-319">クライアントの再接続</span><span class="sxs-lookup"><span data-stu-id="5551e-319">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="5551e-320">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="5551e-320">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="5551e-321">3.0 より前では、の JavaScript クライアントは SignalR 自動的に再接続しません。</span><span class="sxs-lookup"><span data-stu-id="5551e-321">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="5551e-322">クライアントを手動で再接続するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5551e-322">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="5551e-323">次のコードは、標準的な手動再接続アプローチを示しています。</span><span class="sxs-lookup"><span data-stu-id="5551e-323">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="5551e-324">接続を開始する関数 (この場合は `start` 関数) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="5551e-324">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="5551e-325">`start`接続のイベントハンドラーで関数を呼び出し `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="5551e-325">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="5551e-326">実際の実装では、指数バックオフを使用するか、指定された回数の再試行を行います。</span><span class="sxs-lookup"><span data-stu-id="5551e-326">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5551e-327">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="5551e-327">Additional resources</span></span>

* [<span data-ttu-id="5551e-328">JavaScript API リファレンス</span><span class="sxs-lookup"><span data-stu-id="5551e-328">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="5551e-329">JavaScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="5551e-329">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5551e-330">WebPack と TypeScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="5551e-330">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="5551e-331">ハブ</span><span class="sxs-lookup"><span data-stu-id="5551e-331">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="5551e-332">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="5551e-332">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="5551e-333">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="5551e-333">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="5551e-334">クロスオリジン要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="5551e-334">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="5551e-335">Azure SignalR サービスのサーバーレスドキュメント</span><span class="sxs-lookup"><span data-stu-id="5551e-335">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
