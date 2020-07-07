---
title: ASP.NET Core Blazor WebAssembly をデバッグする
author: guardrex
description: Blazor アプリをデバッグする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 9fe51b8c7eafdd62cc6fc1a820135d9ee5ff010e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401013"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="9b251-103">ASP.NET Core Blazor WebAssembly をデバッグする</span><span class="sxs-lookup"><span data-stu-id="9b251-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="9b251-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="9b251-104">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor WebAssembly<span data-ttu-id="9b251-105"> アプリは、Chromium ベースのブラウザー (Edge/Chrome) のブラウザー開発ツールを使用してデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="9b251-105"> apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="9b251-106">または、Visual Studio または Visual Studio Code を使用してアプリをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9b251-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="9b251-107">利用可能なシナリオには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9b251-107">Available scenarios include:</span></span>

* <span data-ttu-id="9b251-108">ブレークポイントの設定と削除を行う。</span><span class="sxs-lookup"><span data-stu-id="9b251-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="9b251-109">Visual Studio と Visual Studio Code (<kbd>F5</kbd> サポート) でデバッグ サポートを使用して、アプリを実行する。</span><span class="sxs-lookup"><span data-stu-id="9b251-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="9b251-110">コードを使用して、シングルステップ (<kbd>F10</kbd>) を実行する。</span><span class="sxs-lookup"><span data-stu-id="9b251-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="9b251-111">ブラウザーで <kbd>F8</kbd> を使用するか、Visual Studio または Visual Studio Code で <kbd>F5</kbd> を使用して、コードの実行を再開する。</span><span class="sxs-lookup"><span data-stu-id="9b251-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="9b251-112">*Locals* の表示で、ローカル変数の値を観察する。</span><span class="sxs-lookup"><span data-stu-id="9b251-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="9b251-113">呼び出し履歴を表示する。これには、JavaScript から .NET への呼び出しチェーンと、.NET から JavaScript への呼び出しチェーンが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9b251-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="9b251-114">現時点では、次の操作を行うことは*できません*。</span><span class="sxs-lookup"><span data-stu-id="9b251-114">For now, you *can't*:</span></span>

* <span data-ttu-id="9b251-115">未処理の例外の発生時に中断する。</span><span class="sxs-lookup"><span data-stu-id="9b251-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="9b251-116">アプリの起動中にブレークポイントにヒットする。</span><span class="sxs-lookup"><span data-stu-id="9b251-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="9b251-117">今後のリリースでは、引き続きデバッグエクス ペリエンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="9b251-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b251-118">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9b251-118">Prerequisites</span></span>

<span data-ttu-id="9b251-119">デバッグには、次のいずれかのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="9b251-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="9b251-120">Microsoft Edge (バージョン 80 以降)</span><span class="sxs-lookup"><span data-stu-id="9b251-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="9b251-121">Google Chrome (バージョン 70 以降)</span><span class="sxs-lookup"><span data-stu-id="9b251-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="9b251-122">Visual Studio と Visual Studio Code のデバッグを有効にする</span><span class="sxs-lookup"><span data-stu-id="9b251-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="9b251-123">既存の Blazor WebAssembly アプリのデバッグを有効にするには、スタートアップ プロジェクトの `launchSettings.json` ファイルを更新して、各起動プロファイルに次の `inspectUri` プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="9b251-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="9b251-124">更新されると、`launchSettings.json` ファイルは次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="9b251-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="9b251-125">`inspectUri` プロパティ:</span><span class="sxs-lookup"><span data-stu-id="9b251-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="9b251-126">アプリが Blazor WebAssembly アプリであることを IDE で検出できるようにします。</span><span class="sxs-lookup"><span data-stu-id="9b251-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="9b251-127">スクリプト デバッグ インフラストラクチャに対して、Blazor のデバッグ プロキシを使用してブラウザーに接続するように指示します。</span><span class="sxs-lookup"><span data-stu-id="9b251-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="9b251-128">起動したブラウザー (`browserInspectUri`) の Websocket プロトコル (`wsProtocol`)、ホスト (`url.hostname`)、ポート (`url.port`)、およびインスペクター URI のプレースホルダー値は、フレームワークによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="9b251-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="9b251-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b251-129">Visual Studio</span></span>

<span data-ttu-id="9b251-130">Visual Studio で Blazor WebAssembly アプリをデバッグするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9b251-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="9b251-131">新しい ASP.NET Core でホストされる Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9b251-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="9b251-132"><kbd>F5</kbd> キーを押して、デバッガーでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b251-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="9b251-133">`Pages/Counter.razor` の `IncrementCount` メソッドにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="9b251-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="9b251-134">**`Counter`** タブに移動し、ボタンを選択してブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="9b251-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![デバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="9b251-136">[ローカル] ウィンドウの `currentCount` フィールドの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="9b251-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![[ローカル] を表示する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="9b251-138"><kbd>F5</kbd> キーを押して、実行を続行します。</span><span class="sxs-lookup"><span data-stu-id="9b251-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="9b251-139">Blazor WebAssembly アプリのデバッグ中に、サーバー コードをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9b251-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="9b251-140">`Pages/FetchData.razor` ページの <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="9b251-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="9b251-141">`Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="9b251-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="9b251-142">**`Fetch Data`** タブに移動し、サーバーへの HTTP 要求を発行する直前に `FetchData` コンポーネント内の最初のブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="9b251-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![フェッチ データをデバッグする](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="9b251-144"><kbd>F5</kbd> キーを押して実行を続行し、`WeatherForecastController` 内のサーバーでブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="9b251-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![デバッグ サーバー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="9b251-146"><kbd>F5</kbd> をもう一度押して実行を続行し、天気予報テーブルがレンダリングされたことを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b251-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="9b251-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b251-147">Visual Studio Code</span></span>

<span data-ttu-id="9b251-148">Visual Studio Code で Blazor WebAssembly アプリをデバッグするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9b251-148">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="9b251-149">`debug.javascript.usePreview` が `true` に設定された状態で、[C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)と [JavaScript デバッガー (夜間)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9b251-149">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![拡張機能](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS プレビュー デバッガー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="9b251-152">スタンドアロン Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="9b251-152">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="9b251-153">VS Code でスタンドアロンの Blazor WebAssembly アプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="9b251-153">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="9b251-154">デバッグを有効にするために追加の設定が必要であるという次の通知が表示された場合は、以下のようにします。</span><span class="sxs-lookup"><span data-stu-id="9b251-154">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="9b251-155">正しい拡張機能がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b251-155">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="9b251-156">JavaScript のプレビュー デバッグが有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b251-156">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="9b251-157">ウィンドウを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9b251-157">Reload the window.</span></span>

   ![追加の設定が必要](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="9b251-159"><kbd>F5</kbd> キーボード ショートカットまたはメニュー項目を使用してデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="9b251-159">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="9b251-160">プロンプトが表示されたら、 **[Blazor WebAssembly Debug]** オプションを選択してデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="9b251-160">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![使用可能なデバッグ オプションの一覧](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="9b251-162">スタンドアロン アプリが起動され、デバッグ ブラウザーが開きます。</span><span class="sxs-lookup"><span data-stu-id="9b251-162">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="9b251-163">`Counter` コンポーネントの `IncrementCount` メソッドにブレークポイントを設定し、ボタンを選択してブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="9b251-163">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code のデバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="9b251-165">ホストされた Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="9b251-165">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="9b251-166">ホストされた Blazor WebAssembly アプリを VS Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="9b251-166">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="9b251-167">プロジェクトの起動構成が設定されていない場合は、次の通知が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9b251-167">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="9b251-168">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b251-168">Select **Yes**.</span></span>

   ![必要なアセットを追加する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="9b251-170">選択ウィンドウで、ホストされているソリューション内の *Server* プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="9b251-170">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="9b251-171">`launch.json` ファイルが、デバッガーを起動するための起動構成を使用して生成されます。</span><span class="sxs-lookup"><span data-stu-id="9b251-171">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="9b251-172">既存のデバッグセッションにアタッチする</span><span class="sxs-lookup"><span data-stu-id="9b251-172">Attach to an existing debugging session</span></span>

<span data-ttu-id="9b251-173">実行中の Blazor アプリにアタッチするには、次のように構成された `launch.json` ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9b251-173">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="9b251-174">デバッグ セッションへのアタッチは、スタンドアロン アプリでのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9b251-174">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="9b251-175">フルスタック デバッグを使用するには、VS Code からアプリを起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b251-175">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="9b251-176">起動構成のオプション</span><span class="sxs-lookup"><span data-stu-id="9b251-176">Launch configuration options</span></span>

<span data-ttu-id="9b251-177">`blazorwasm` デバッグの種類では、次の起動構成オプションがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9b251-177">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="9b251-178">オプション</span><span class="sxs-lookup"><span data-stu-id="9b251-178">Option</span></span>    | <span data-ttu-id="9b251-179">説明</span><span class="sxs-lookup"><span data-stu-id="9b251-179">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="9b251-180">`launch` を使用して、Blazor WebAssembly アプリにデバッグ セッションを起動してアタッチするか、`attach` を使用して既に実行中のアプリにデバッグ セッションをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="9b251-180">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="9b251-181">デバッグ時にブラウザーで開く URL。</span><span class="sxs-lookup"><span data-stu-id="9b251-181">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="9b251-182">既定値は `https://localhost:5001` です。</span><span class="sxs-lookup"><span data-stu-id="9b251-182">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="9b251-183">デバッグ セッション用に起動するブラウザー。</span><span class="sxs-lookup"><span data-stu-id="9b251-183">The browser to launch for the debugging session.</span></span> <span data-ttu-id="9b251-184">`edge` または `chrome` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b251-184">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="9b251-185">既定値は `chrome` です。</span><span class="sxs-lookup"><span data-stu-id="9b251-185">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="9b251-186">JS デバッガーからログを生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b251-186">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="9b251-187">ログを生成するには `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b251-187">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="9b251-188">ホストされている Blazor WebAssembly アプリを起動およびデバッグする場合は、`true` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b251-188">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="9b251-189">Web サーバーの絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9b251-189">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="9b251-190">アプリをサブルートから提供する場合は設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b251-190">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="9b251-191">デバッグ セッションがアタッチされるのを待機するミリ秒単位の時間。</span><span class="sxs-lookup"><span data-stu-id="9b251-191">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="9b251-192">既定値は 30,000 ミリ秒 (30 秒) です。</span><span class="sxs-lookup"><span data-stu-id="9b251-192">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="9b251-193">ホストされたアプリのサーバーを実行する実行可能ファイルへの参照。</span><span class="sxs-lookup"><span data-stu-id="9b251-193">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="9b251-194">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b251-194">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="9b251-195">アプリを起動する作業ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="9b251-195">The working directory to launch the app under.</span></span> <span data-ttu-id="9b251-196">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b251-196">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="9b251-197">起動されたプロセスに提供する環境変数。</span><span class="sxs-lookup"><span data-stu-id="9b251-197">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="9b251-198">`hosted` が `true`に設定されている場合にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="9b251-198">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="9b251-199">起動構成の例</span><span class="sxs-lookup"><span data-stu-id="9b251-199">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="9b251-200">スタンドアロン Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="9b251-200">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="9b251-201">指定された URL で実行中のアプリにアタッチする</span><span class="sxs-lookup"><span data-stu-id="9b251-201">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="9b251-202">ホストされている Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="9b251-202">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="9b251-203">ブラウザーでデバッグする</span><span class="sxs-lookup"><span data-stu-id="9b251-203">Debug in the browser</span></span>

1. <span data-ttu-id="9b251-204">開発環境でアプリのデバッグ ビルドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b251-204">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="9b251-205"><kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd> キーを押します。</span><span class="sxs-lookup"><span data-stu-id="9b251-205">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="9b251-206">リモート デバッグが有効な状態で、ブラウザーを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b251-206">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="9b251-207">リモート デバッグが無効になっている場合、 **[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** というエラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9b251-207">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="9b251-208">エラー ページには、Blazor デバッグ プロキシがアプリに接続できるように、デバッグ ポートを開いた状態でブラウザーを実行するための手順が記載されています。</span><span class="sxs-lookup"><span data-stu-id="9b251-208">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="9b251-209">*すべてのブラウザー インスタンス*を閉じ、指示に従ってブラウザーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="9b251-209">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="9b251-210">リモート デバッグが有効な状態でブラウザーを実行すると、デバッグ用のキーボード ショートカットによって新しいデバッガー タブが開きます。少しすると、 **[ソース]** タブに、アプリ内の .NET アセンブリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9b251-210">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="9b251-211">各アセンブリを展開して、デバッグに使用できる `.cs`/`.razor` ソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="9b251-211">Expand each assembly and find the `.cs`/`.razor` source files available for debugging.</span></span> <span data-ttu-id="9b251-212">ブレークポイントを設定し、アプリのタブに戻ります。コードの実行時にブレークポイントにヒットします。</span><span class="sxs-lookup"><span data-stu-id="9b251-212">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="9b251-213">ブレークポイントにヒットした後、コード全体をステップ実行する (<kbd>F10</kbd>) か、コードの実行を普通に再開 (<kbd>F8</kbd>) します。</span><span class="sxs-lookup"><span data-stu-id="9b251-213">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="9b251-214"> は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。</span><span class="sxs-lookup"><span data-stu-id="9b251-214"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="9b251-215">デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。</span><span class="sxs-lookup"><span data-stu-id="9b251-215">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="9b251-216">プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。</span><span class="sxs-lookup"><span data-stu-id="9b251-216">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="9b251-217">ブラウザー ソース マップ</span><span class="sxs-lookup"><span data-stu-id="9b251-217">Browser source maps</span></span>

<span data-ttu-id="9b251-218">ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b251-218">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="9b251-219">ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。</span><span class="sxs-lookup"><span data-stu-id="9b251-219">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="9b251-220">その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。</span><span class="sxs-lookup"><span data-stu-id="9b251-220">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9b251-221">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="9b251-221">Troubleshoot</span></span>

<span data-ttu-id="9b251-222">エラーが発生している場合は、次のヒントが役立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="9b251-222">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="9b251-223">**[デバッガー]** タブで、ブラウザー内の開発者ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="9b251-223">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="9b251-224">コンソールで `localStorage.clear()` を実行して、任意のブレークポイントを削除します。</span><span class="sxs-lookup"><span data-stu-id="9b251-224">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="9b251-225">ASP.NET Core HTTPS 開発証明書がインストールされ、信頼されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b251-225">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="9b251-226">詳細については、「<xref:security/enforcing-ssl#troubleshoot-certificate-problems>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b251-226">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="9b251-227">Visual Studio では、 **[ツール]**  >  **[オプション]**  >  **[デバッグ]**  >  **[全般]** で **[Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)] (ASP.NET (Chrome、Edge、IE) の JavaScript デバッグを有効にする)** オプションをオンにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b251-227">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="9b251-228">これは、Visual Studio の既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="9b251-228">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="9b251-229">デバッグが機能していない場合は、オプションがオンになっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b251-229">If debugging isn't working, confirm that the option is selected.</span></span>
