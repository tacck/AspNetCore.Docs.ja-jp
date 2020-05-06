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
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="a41ce-103">ASP.NET Core SignalR JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="a41ce-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="a41ce-104">作成者: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="a41ce-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="a41ce-105">ASP.NET Core SignalR JavaScript クライアントライブラリを使用すると、開発者はサーバー側のハブコードを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="a41ce-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a41ce-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="a41ce-107">クライアントパッケージSignalRをインストールする</span><span class="sxs-lookup"><span data-stu-id="a41ce-107">Install the SignalR client package</span></span>

<span data-ttu-id="a41ce-108">SignalR JavaScript クライアントライブラリは[npm](https://www.npmjs.com/)パッケージとして配信されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="a41ce-109">次のセクションでは、さまざまな方法でクライアントライブラリをインストールする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="a41ce-110">Npm を使用してインストールする</span><span class="sxs-lookup"><span data-stu-id="a41ce-110">Install with npm</span></span>

<span data-ttu-id="a41ce-111">Visual Studio を使用している場合は、ルートフォルダーで、**パッケージマネージャーコンソール**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="a41ce-112">Visual Studio Code には、**統合ターミナル**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="a41ce-113">npm は、 \*node_modules\\ \*フォルダーにパッケージの内容をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a41ce-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="a41ce-114">*Wwwroot\\lib*フォルダーの下に*signalr*という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="a41ce-115">*Signalr*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="a41ce-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="a41ce-116">npm は、 \*node_modules\\ \*フォルダーにパッケージの内容をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a41ce-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="a41ce-117">*Wwwroot\\lib*フォルダーの下に*signalr*という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="a41ce-118">*Signalr*ファイルを*wwwroot\lib\signalr*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="a41ce-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="a41ce-119">要素でSignalR JavaScript クライアントを参照します。 `<script>`</span><span class="sxs-lookup"><span data-stu-id="a41ce-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="a41ce-120">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="a41ce-121">Content Delivery Network を使用する (CDN)</span><span class="sxs-lookup"><span data-stu-id="a41ce-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="a41ce-122">Npm の前提条件なしでクライアントライブラリを使用するには、CDN でホストされるクライアントライブラリのコピーを参照します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="a41ce-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="a41ce-124">クライアントライブラリは、次の CDNs で利用できます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="a41ce-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="a41ce-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="a41ce-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="a41ce-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="a41ce-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="a41ce-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="a41ce-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="a41ce-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="a41ce-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="a41ce-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="a41ce-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="a41ce-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="a41ce-131">LibMan でのインストール</span><span class="sxs-lookup"><span data-stu-id="a41ce-131">Install with LibMan</span></span>

<span data-ttu-id="a41ce-132">[Libman](xref:client-side/libman/index)を使用すると、CDN でホストされているクライアントライブラリから特定のクライアントライブラリファイルをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="a41ce-133">たとえば、縮小版の JavaScript ファイルのみをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="a41ce-134">その方法の詳細については、「[クライアントライブラリのSignalR追加](xref:tutorials/signalr#add-the-signalr-client-library)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41ce-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="a41ce-135">ハブへの接続</span><span class="sxs-lookup"><span data-stu-id="a41ce-135">Connect to a hub</span></span>

<span data-ttu-id="a41ce-136">次のコードでは、接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="a41ce-137">ハブの名前は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="a41ce-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="a41ce-138">クロスオリジン接続</span><span class="sxs-lookup"><span data-stu-id="a41ce-138">Cross-origin connections</span></span>

<span data-ttu-id="a41ce-139">通常、ブラウザーは、要求されたページと同じドメインからの接続を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="a41ce-140">ただし、別のドメインへの接続が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="a41ce-141">悪意のあるサイトが別のサイトから機密データを読み取れないようにするため、既定では、[クロスオリジン接続](xref:security/cors)は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="a41ce-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="a41ce-142">クロスオリジン要求を許可するには、 `Startup`クラスで有効にします。</span><span class="sxs-lookup"><span data-stu-id="a41ce-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="a41ce-143">クライアントからのハブメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="a41ce-143">Call hub methods from client</span></span>

<span data-ttu-id="a41ce-144">JavaScript クライアントは、 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)の[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)メソッドを使用して、ハブでパブリックメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="a41ce-145">メソッド`invoke`は、次の2つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="a41ce-146">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="a41ce-146">The name of the hub method.</span></span> <span data-ttu-id="a41ce-147">次の例では、ハブのメソッド名は`SendMessage`です。</span><span class="sxs-lookup"><span data-stu-id="a41ce-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="a41ce-148">ハブメソッドで定義されているすべての引数。</span><span class="sxs-lookup"><span data-stu-id="a41ce-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="a41ce-149">次の例では、引数名は`message`です。</span><span class="sxs-lookup"><span data-stu-id="a41ce-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="a41ce-150">このコード例では、Internet Explorer を除くすべての主要なブラウザーの現在のバージョンでサポートされている、矢印関数の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="a41ce-151">*サーバーレスモード*で Azure SignalRサービスを使用している場合は、クライアントからハブメソッドを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="a41ce-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="a41ce-152">詳細については、 [ SignalRサービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41ce-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="a41ce-153">メソッド`invoke`は、JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="a41ce-154">は`Promise` 、サーバーのメソッドがを返すときに戻り値 (存在する場合) を使用して解決されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="a41ce-155">サーバー上のメソッドがエラーをスローした場合`Promise` 、はエラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a41ce-156">これらの`then`ケース`catch` (または`Promise` `await`構文) を処理するには、自身でメソッドとメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="a41ce-157">メソッド`send`は、JavaScript `Promise`を返します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="a41ce-158">は`Promise` 、メッセージがサーバーに送信されたときに解決されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="a41ce-159">メッセージの送信中にエラーが発生した`Promise`場合、はエラーメッセージと共に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a41ce-160">これらの`then`ケース`catch` (または`Promise` `await`構文) を処理するには、自身でメソッドとメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="a41ce-161">を`send`使用すると、サーバーがメッセージを受信するまで待機しません。</span><span class="sxs-lookup"><span data-stu-id="a41ce-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="a41ce-162">このため、サーバーからデータまたはエラーを返すことはできません。</span><span class="sxs-lookup"><span data-stu-id="a41ce-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="a41ce-163">ハブからクライアントメソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="a41ce-163">Call client methods from hub</span></span>

<span data-ttu-id="a41ce-164">ハブからメッセージを受信するには、の[on](/javascript/api/%40aspnet/signalr/hubconnection#on)メソッドを使用して`HubConnection`メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="a41ce-165">JavaScript クライアントメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="a41ce-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="a41ce-166">次の例では、メソッド名は`ReceiveMessage`です。</span><span class="sxs-lookup"><span data-stu-id="a41ce-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="a41ce-167">ハブがメソッドに渡す引数。</span><span class="sxs-lookup"><span data-stu-id="a41ce-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="a41ce-168">次の例では、引数の値`message`はです。</span><span class="sxs-lookup"><span data-stu-id="a41ce-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="a41ce-169">の`connection.on`前のコードは、サーバー側のコードが[sendasync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)メソッドを使用して呼び出したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="a41ce-170">と`SendAsync` `connection.on`で定義されたメソッド名と引数を照合することによって、どのクライアントメソッドを呼び出すかを決定します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="a41ce-171">ベストプラクティスとして、の後`HubConnection` `on`にの[start](/javascript/api/%40aspnet/signalr/hubconnection#start)メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="a41ce-172">これにより、メッセージを受信する前にハンドラーが確実に登録されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="a41ce-173">エラー処理とログ記録</span><span class="sxs-lookup"><span data-stu-id="a41ce-173">Error handling and logging</span></span>

<span data-ttu-id="a41ce-174">メソッドを`catch` `start`メソッドの最後にチェーンして、クライアント側のエラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="a41ce-175">ブラウザー `console.error`のコンソールにエラーを出力するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="a41ce-176">接続が確立されたときにログに記録する logger と種類のイベントを渡すことによって、クライアント側のログトレースを設定します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="a41ce-177">メッセージは、指定されたログレベル以上でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="a41ce-178">使用可能なログレベルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a41ce-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="a41ce-179">`signalR.LogLevel.Error`&ndash;エラーメッセージ。</span><span class="sxs-lookup"><span data-stu-id="a41ce-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="a41ce-180">メッセージ`Error`のみをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="a41ce-181">`signalR.LogLevel.Warning`&ndash;潜在的なエラーに関する警告メッセージ。</span><span class="sxs-lookup"><span data-stu-id="a41ce-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="a41ce-182">ログ`Warning`と`Error`メッセージ。</span><span class="sxs-lookup"><span data-stu-id="a41ce-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a41ce-183">`signalR.LogLevel.Information`&ndash;エラーのないステータスメッセージ。</span><span class="sxs-lookup"><span data-stu-id="a41ce-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="a41ce-184">、 `Information`、 `Warning`および`Error`の各メッセージをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a41ce-185">`signalR.LogLevel.Trace`&ndash;トレースメッセージ。</span><span class="sxs-lookup"><span data-stu-id="a41ce-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="a41ce-186">ハブとクライアント間で転送されたデータを含む、すべてをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="a41ce-187">ログレベルを構成するには、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)で[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="a41ce-188">メッセージは、ブラウザーコンソールに記録されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="a41ce-189">クライアントの再接続</span><span class="sxs-lookup"><span data-stu-id="a41ce-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="a41ce-190">自動的に再接続する</span><span class="sxs-lookup"><span data-stu-id="a41ce-190">Automatically reconnect</span></span>

<span data-ttu-id="a41ce-191">のSignalR JavaScript クライアントは、 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)のメソッドを使用して自動的に再接続するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="a41ce-192">既定では、自動的に再接続されません。</span><span class="sxs-lookup"><span data-stu-id="a41ce-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="a41ce-193">パラメーターを指定し`withAutomaticReconnect()`ない場合、は、再接続を試行する前に、それぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="a41ce-194">再接続の試行を開始する`HubConnection`前に、は`HubConnectionState.Reconnecting`状態に遷移し`onreconnecting` 、コールバックを起動し`Disconnected`ます。この状態`onclose`に遷移し`HubConnection` 、自動再接続が構成されていないなどのコールバックをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41ce-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="a41ce-195">これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="a41ce-196">最初の4回の試行でクライアントが正常に再`HubConnection`接続した場合、 `Connected`は状態に戻り`onreconnected` 、コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="a41ce-197">これにより、接続が再確立されたことをユーザーに通知することができます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="a41ce-198">接続はサーバーにまったく新しいものであるため、 `connectionId` `onreconnected`コールバックに新しいが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="a41ce-199">が`onreconnected`ネゴシエーションを`connectionId` [スキップ](xref:signalr/configuration#configure-client-options)するように構成さ`HubConnection`れている場合、コールバックのパラメーターは未定義になります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="a41ce-200">`withAutomaticReconnect()`最初の開始`HubConnection`エラーを再試行するようにを構成しません。そのため、開始エラーは手動で処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="a41ce-201">最初の4回の試行でクライアントが正常に再接続`HubConnection`されない場合`Disconnected` 、は状態に遷移し、その[閉じる](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバックを起動します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="a41ce-202">これにより、接続が完全に失われたことをユーザーに通知し、ページを最新の情報に更新することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a41ce-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="a41ce-203">再接続のタイミングを切断または変更する前に、カスタムの再接続試行`withAutomaticReconnect`回数を構成するために、では、各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="a41ce-204">前の例では`HubConnection` 、接続が失われた直後に再接続を開始するようにを構成しています。</span><span class="sxs-lookup"><span data-stu-id="a41ce-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="a41ce-205">これは、既定の構成にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="a41ce-206">最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="a41ce-207">2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="a41ce-208">このカスタム動作は、既定の構成のように、別の30秒でもう一度再接続を試行するのではなく、3回目の再接続試行の失敗後に停止することで、既定の動作から再び逸脱します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="a41ce-209">タイミングと自動再接続試行回数をさらに細かく制御する場合は、 `withAutomaticReconnect` `IRetryPolicy`インターフェイスを実装するオブジェクトを受け取ります。このインターフェイスには`nextRetryDelayInMilliseconds`、という名前の1つのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="a41ce-210">`nextRetryDelayInMilliseconds`型`RetryContext`の1つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="a41ce-211">に`RetryContext`は、、、 `previousRetryCount`および`elapsedMilliseconds`の`retryReason` 3 つの`number`プロパティ`number` `Error`があります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="a41ce-212">最初の再接続を試行する`previousRetryCount`前`elapsedMilliseconds`に、との`retryReason`両方がゼロになり、は接続が失われる原因となったエラーになります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="a41ce-213">再試行に失敗するたびに`previousRetryCount` 、が 1 `elapsedMilliseconds`ずつインクリメントされます。これまでの再接続にかかった時間 (ミリ秒単位) を`retryReason`反映して更新されます。このエラーは、最後の再接続の試行が失敗した原因となったエラーになります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="a41ce-214">`nextRetryDelayInMilliseconds`は、次回の再接続が試行される前に待機するミリ秒数を`null`表す数値`HubConnection`を返すか、再接続を停止する必要がある場合はを返します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="a41ce-215">または、[手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="a41ce-216">手動で再接続する</span><span class="sxs-lookup"><span data-stu-id="a41ce-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="a41ce-217">3.0 より前では、のSignalR JavaScript クライアントは自動的に再接続しません。</span><span class="sxs-lookup"><span data-stu-id="a41ce-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="a41ce-218">クライアントを手動で再接続するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41ce-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="a41ce-219">次のコードは、標準的な手動再接続アプローチを示しています。</span><span class="sxs-lookup"><span data-stu-id="a41ce-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="a41ce-220">接続を開始する関数 (この場合`start`は関数) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="a41ce-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="a41ce-221">接続の`start` `onclose`イベントハンドラーで関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a41ce-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="a41ce-222">実際の実装では、指数バックオフを使用するか、指定された回数の再試行を行います。</span><span class="sxs-lookup"><span data-stu-id="a41ce-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a41ce-223">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a41ce-223">Additional resources</span></span>

* [<span data-ttu-id="a41ce-224">JavaScript API リファレンス</span><span class="sxs-lookup"><span data-stu-id="a41ce-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="a41ce-225">JavaScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="a41ce-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a41ce-226">WebPack と TypeScript のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="a41ce-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="a41ce-227">ハブ</span><span class="sxs-lookup"><span data-stu-id="a41ce-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a41ce-228">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="a41ce-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a41ce-229">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="a41ce-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="a41ce-230">クロスオリジン要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="a41ce-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="a41ce-231">[Azure SignalRサービスのサーバーレスドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="a41ce-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
