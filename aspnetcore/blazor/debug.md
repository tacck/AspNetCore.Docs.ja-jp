---
title: "title:'ASP.NET Core Blazor WebAssembly のデバッグ' author: guardrex description:'Blazor アプリをデバッグする方法について説明します。'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date:05/31/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239207"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="c6cdd-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6cdd-103">'Blazor'</span></span>

<span data-ttu-id="c6cdd-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6cdd-104">'Identity'</span></span>

<span data-ttu-id="c6cdd-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6cdd-105">'Let's Encrypt'</span></span> <span data-ttu-id="c6cdd-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6cdd-106">'Razor'</span></span>

<span data-ttu-id="c6cdd-107">'SignalR' uid: blazor/debug</span><span class="sxs-lookup"><span data-stu-id="c6cdd-107">'SignalR' uid: blazor/debug</span></span>

* <span data-ttu-id="c6cdd-108">ASP.NET Core Blazor WebAssembly をデバッグする</span><span class="sxs-lookup"><span data-stu-id="c6cdd-108">Debug ASP.NET Core Blazor WebAssembly</span></span>
* [<span data-ttu-id="c6cdd-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="c6cdd-109">Daniel Roth</span></span>](https://github.com/danroth27)
* Blazor<span data-ttu-id="c6cdd-110"> WebAssembly アプリは、Chromium ベースのブラウザー (Edge/Chrome) のブラウザー開発ツールを使用してデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>
* <span data-ttu-id="c6cdd-111">または、Visual Studio または Visual Studio Code を使用してアプリをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="c6cdd-112">利用可能なシナリオには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-112">Available scenarios include:</span></span>
* <span data-ttu-id="c6cdd-113">ブレークポイントの設定と削除を行う。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-113">Set and remove breakpoints.</span></span>

<span data-ttu-id="c6cdd-114">Visual Studio と Visual Studio Code (<kbd>F5</kbd> サポート) でデバッグ サポートを使用して、アプリを実行する。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>

* <span data-ttu-id="c6cdd-115">コードを使用して、シングルステップ (<kbd>F10</kbd>) を実行する。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="c6cdd-116">ブラウザーで <kbd>F8</kbd> を使用するか、Visual Studio または Visual Studio Code で <kbd>F5</kbd> を使用して、コードの実行を再開する。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="c6cdd-117">*Locals* の表示で、ローカル変数の値を観察する。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-117">In the *Locals* display, observe the values of local variables.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c6cdd-118">呼び出し履歴を表示する。これには、JavaScript から .NET への呼び出しチェーンと、.NET から JavaScript への呼び出しチェーンが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="c6cdd-119">現時点では、次の操作を行うことは*できません*。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-119">For now, you *can't*:</span></span>

* <span data-ttu-id="c6cdd-120">未処理の例外の発生時に中断する。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="c6cdd-121">アプリの起動中にブレークポイントにヒットする。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-121">Hit breakpoints during app startup.</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="c6cdd-122">今後のリリースでは、引き続きデバッグエクス ペリエンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

<span data-ttu-id="c6cdd-123">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c6cdd-123">Prerequisites</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="c6cdd-124">デバッグには、次のいずれかのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-124">Debugging requires either of the following browsers:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="c6cdd-125">Microsoft Edge (バージョン 80 以降)</span><span class="sxs-lookup"><span data-stu-id="c6cdd-125">Microsoft Edge (version 80 or later)</span></span>

* <span data-ttu-id="c6cdd-126">Google Chrome (バージョン 70 以降)</span><span class="sxs-lookup"><span data-stu-id="c6cdd-126">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="c6cdd-127">Visual Studio と Visual Studio Code のデバッグを有効にする</span><span class="sxs-lookup"><span data-stu-id="c6cdd-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="c6cdd-128">既存の Blazor WebAssembly アプリのデバッグを有効にするには、スタートアップ プロジェクトの *launchSettings. json* ファイルを更新して、各起動プロファイルに次の `inspectUri` プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

## <a name="visual-studio"></a><span data-ttu-id="c6cdd-129">更新されると、*launchSettings. json* ファイルは次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

<span data-ttu-id="c6cdd-130">`inspectUri` プロパティ:</span><span class="sxs-lookup"><span data-stu-id="c6cdd-130">The `inspectUri` property:</span></span>

1. <span data-ttu-id="c6cdd-131">アプリが Blazor WebAssembly アプリであることを IDE で検出できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="c6cdd-132">スクリプト デバッグ インフラストラクチャに対して、Blazor のデバッグ プロキシを使用してブラウザーに接続するように指示します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>
1. <span data-ttu-id="c6cdd-133">起動したブラウザー (`browserInspectUri`) の Websocket プロトコル (`wsProtocol`)、ホスト (`url.hostname`)、ポート (`url.port`)、およびインスペクター URI のプレースホルダー値は、フレームワークによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>
1. <span data-ttu-id="c6cdd-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6cdd-134">Visual Studio</span></span>

   ![Visual Studio で Blazor WebAssembly アプリをデバッグするには、次のようにします。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="c6cdd-136">ASP.NET Core でホストされる新しい Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>

   ![<kbd>F5</kbd> キーを押して、デバッガーでアプリを実行します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="c6cdd-138">`IncrementCount` メソッドで *Counter.razor* にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>

<span data-ttu-id="c6cdd-139">**[カウンター]** タブに移動し、ボタンを選択してブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

1. <span data-ttu-id="c6cdd-140">デバッグ カウンター</span><span class="sxs-lookup"><span data-stu-id="c6cdd-140">Debug Counter</span></span>
1. <span data-ttu-id="c6cdd-141">[ローカル] ウィンドウの `currentCount` フィールドの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-141">Check out the value of the `currentCount` field in the locals window:</span></span>
1. <span data-ttu-id="c6cdd-142">[ローカル] を表示する</span><span class="sxs-lookup"><span data-stu-id="c6cdd-142">View locals</span></span>

   ![<kbd>F5</kbd> キーを押して、実行を続行します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="c6cdd-144">Blazor WebAssembly アプリのデバッグ中に、サーバー コードをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

   ![<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> の *FetchData.razor* ページにブレークポイントを設定します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="c6cdd-146">`Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="c6cdd-147">**フェッチ データ** タブに移動し、サーバーへの HTTP 要求を発行する直前に `FetchData` コンポーネント内の最初のブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

<span data-ttu-id="c6cdd-148">フェッチ データをデバッグする</span><span class="sxs-lookup"><span data-stu-id="c6cdd-148">Debug Fetch Data</span></span>
 
<span data-ttu-id="c6cdd-149"><kbd>F5</kbd> キーを押して実行を続行し、`WeatherForecastController` 内のサーバーでブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

![デバッグ サーバー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![<kbd>F5</kbd> をもう一度押して実行を続行し、天気予報テーブルがレンダリングされたことを確認します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="c6cdd-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6cdd-152">Visual Studio Code</span></span>

1. <span data-ttu-id="c6cdd-153">Visual Studio Code で Blazor WebAssembly アプリをデバッグするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>

   <span data-ttu-id="c6cdd-154">`debug.javascript.usePreview` が `true` に設定された状態で、[C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)と [JavaScript デバッガー (夜間)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>
   
   * <span data-ttu-id="c6cdd-155">拡張機能</span><span class="sxs-lookup"><span data-stu-id="c6cdd-155">Extensions</span></span>
   * <span data-ttu-id="c6cdd-156">JS プレビュー デバッガー</span><span class="sxs-lookup"><span data-stu-id="c6cdd-156">JS preview debugger</span></span>
   * <span data-ttu-id="c6cdd-157">スタンドアロンの Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="c6cdd-157">Debug standalone Blazor WebAssembly</span></span>

   ![VS Code でスタンドアロンの Blazor WebAssembly アプリを開きます。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="c6cdd-159">デバッグを有効にするために追加の設定が必要であるという次の通知が表示された場合は、以下のようにします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-159">If you receive the following notification that additional setup is required to enable debugging:</span></span>

1. <span data-ttu-id="c6cdd-160">正しい拡張機能がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-160">Confirm that you have the correct extensions installed.</span></span>

   ![JavaScript のプレビュー デバッグが有効になっていることを確認します。](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="c6cdd-162">ウィンドウを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-162">Reload the window.</span></span>

1. <span data-ttu-id="c6cdd-163">追加の設定が必要</span><span class="sxs-lookup"><span data-stu-id="c6cdd-163">Additional setup required</span></span>

   ![<kbd>F5</kbd> キーボード ショートカットまたはメニュー項目を使用してデバッグを開始します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="c6cdd-165">プロンプトが表示されたら、 **[Blazor WebAssembly Debug]** オプションを選択してデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-165">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="c6cdd-166">使用可能なデバッグ オプションの一覧</span><span class="sxs-lookup"><span data-stu-id="c6cdd-166">List of available debug options</span></span>

1. <span data-ttu-id="c6cdd-167">スタンドアロン アプリが起動され、デバッグ ブラウザーが開きます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-167">The standalone app is launched, and a debugging browser is opened.</span></span> <span data-ttu-id="c6cdd-168">`Counter` コンポーネントの `IncrementCount` メソッドにブレークポイントを設定し、ボタンを選択してブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code のデバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="c6cdd-170">ホストされた Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="c6cdd-170">Debug hosted Blazor WebAssembly</span></span>

<span data-ttu-id="c6cdd-171">ホストされた Blazor WebAssembly アプリを VS Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-171">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="c6cdd-172">プロジェクトの起動構成が設定されていない場合は、次の通知が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-172">If there's no launch configuration set for the project, the following notification appears.</span></span>

<span data-ttu-id="c6cdd-173">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-173">Select **Yes**.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="c6cdd-174">必要なアセットを追加する</span><span class="sxs-lookup"><span data-stu-id="c6cdd-174">Add required assets</span></span> <span data-ttu-id="c6cdd-175">選択ウィンドウで、ホストされているソリューション内の *Server* プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-175">In the selection window, select the *Server* project within the hosted solution.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="c6cdd-176">*launch.json* ファイルが、デバッガーを起動するための起動構成を使用して生成されます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-176">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

<span data-ttu-id="c6cdd-177">既存のデバッグセッションにアタッチする</span><span class="sxs-lookup"><span data-stu-id="c6cdd-177">Attach to an existing debugging session</span></span>

| <span data-ttu-id="c6cdd-178">実行中の Blazor アプリにアタッチするには、次のように構成された *launch.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-178">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>    | <span data-ttu-id="c6cdd-179">デバッグ セッションへのアタッチは、スタンドアロン アプリでのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-179">Attaching to a debugging session is only supported for standalone apps.</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="c6cdd-180">フルスタック デバッグを使用するには、VS Code からアプリを起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-180">To use full-stack debugging, you must launch the app from VS Code.</span></span> |
| `url`     | <span data-ttu-id="c6cdd-181">起動構成のオプション</span><span class="sxs-lookup"><span data-stu-id="c6cdd-181">Launch configuration options</span></span> <span data-ttu-id="c6cdd-182">`blazorwasm` デバッグの種類では、次の起動構成オプションがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-182">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span> |
| `browser` | <span data-ttu-id="c6cdd-183">オプション</span><span class="sxs-lookup"><span data-stu-id="c6cdd-183">Option</span></span> <span data-ttu-id="c6cdd-184">説明</span><span class="sxs-lookup"><span data-stu-id="c6cdd-184">Description</span></span> <span data-ttu-id="c6cdd-185">`launch` を使用して、Blazor WebAssembly アプリにデバッグ セッションを起動してアタッチするか、`attach` を使用して既に実行中のアプリにデバッグセッションをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-185">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `trace`   | <span data-ttu-id="c6cdd-186">デバッグ時にブラウザーで開く URL。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-186">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="c6cdd-187">既定値は `https://localhost:5001` です。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-187">Defaults to `https://localhost:5001`.</span></span> |
| `hosted`  | <span data-ttu-id="c6cdd-188">デバッグ セッション用に起動するブラウザー。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-188">The browser to launch for the debugging session.</span></span> |
| `webRoot` | <span data-ttu-id="c6cdd-189">`edge` または `chrome` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-189">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="c6cdd-190">既定値は `chrome` です。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-190">Defaults to `chrome`.</span></span> |
| `timeout` | <span data-ttu-id="c6cdd-191">JS デバッガーからログを生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-191">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="c6cdd-192">ログを生成するには `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-192">Set to `true` to generate logs.</span></span> |
| `program` | <span data-ttu-id="c6cdd-193">ホストされている Blazor WebAssembly アプリを起動およびデバッグする場合は、`true` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-193">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> <span data-ttu-id="c6cdd-194">Web サーバーの絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-194">Specifies the absolute path of the web server.</span></span> |
| `cwd`     | <span data-ttu-id="c6cdd-195">アプリをサブルートから提供する場合は設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-195">Should be set if an app is served from a sub-route.</span></span> <span data-ttu-id="c6cdd-196">デバッグ セッションがアタッチされるのを待機するミリ秒単位の時間。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-196">The number of milliseconds to wait for the debugging session to attach.</span></span> |
| `env`     | <span data-ttu-id="c6cdd-197">既定値は 30,000 ミリ秒 (30 秒) です。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-197">Defaults to 30,000 milliseconds (30 seconds).</span></span> <span data-ttu-id="c6cdd-198">ホストされたアプリのサーバーを実行する実行可能ファイルへの参照。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-198">A reference to the executable to run the server of the hosted app.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="c6cdd-199">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-199">Must be set if `hosted` is `true`.</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="c6cdd-200">アプリを起動する作業ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-200">The working directory to launch the app under.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="c6cdd-201">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-201">Must be set if `hosted` is `true`.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="c6cdd-202">起動されたプロセスに提供する環境変数。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-202">The environment variables to provide to the launched process.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="c6cdd-203">`hosted` が `true`に設定されている場合にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-203">Only applicable if `hosted` is set to `true`.</span></span>

1. <span data-ttu-id="c6cdd-204">起動構成の例</span><span class="sxs-lookup"><span data-stu-id="c6cdd-204">Example launch configurations</span></span>

1. <span data-ttu-id="c6cdd-205">スタンドアロン Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="c6cdd-205">Launch and debug a standalone Blazor WebAssembly app</span></span>

1. <span data-ttu-id="c6cdd-206">指定された URL で実行中のアプリにアタッチする</span><span class="sxs-lookup"><span data-stu-id="c6cdd-206">Attach to a running app at a specified URL</span></span> <span data-ttu-id="c6cdd-207">ホストされている Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="c6cdd-207">Launch and debug a hosted Blazor WebAssembly app</span></span> <span data-ttu-id="c6cdd-208">ブラウザーでデバッグする</span><span class="sxs-lookup"><span data-stu-id="c6cdd-208">Debug in the browser</span></span> <span data-ttu-id="c6cdd-209">開発環境でアプリのデバッグ ビルドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-209">Run a Debug build of the app in the Development environment.</span></span>

<span data-ttu-id="c6cdd-210"><kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd> キーを押します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-210">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span> <span data-ttu-id="c6cdd-211">リモート デバッグが有効な状態で、ブラウザーを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-211">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="c6cdd-212">リモート デバッグが無効になっている場合、 **[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** というエラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-212">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="c6cdd-213">エラー ページには、Blazor デバッグ プロキシがアプリに接続できるように、デバッグ ポートを開いた状態でブラウザーを実行するための手順が記載されています。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-213">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span>

<span data-ttu-id="c6cdd-214">*すべてのブラウザー インスタンス*を閉じ、指示に従ってブラウザーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-214">*Close all browser instances* and restart the browser as instructed.</span></span> <span data-ttu-id="c6cdd-215">リモート デバッグが有効な状態でブラウザーを実行すると、デバッグ用のキーボード ショートカットによって新しいデバッガー タブが開きます。少しすると、 **[ソース]** タブに、アプリ内の .NET アセンブリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-215">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="c6cdd-216">各アセンブリを展開して、デバッグに使用できる *.cs*/ *.razor* ソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-216">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="c6cdd-217">ブレークポイントを設定し、アプリのタブに戻ります。コードの実行時にブレークポイントにヒットします。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-217">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span>

<span data-ttu-id="c6cdd-218">ブレークポイントにヒットした後、コード全体をステップ実行する (<kbd>F10</kbd>) か、コードの実行を普通に再開 (<kbd>F8</kbd>) します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-218">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span> Blazor<span data-ttu-id="c6cdd-219"> は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-219"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="c6cdd-220">デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-220">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c6cdd-221">プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-221">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

<span data-ttu-id="c6cdd-222">ブラウザー ソース マップ</span><span class="sxs-lookup"><span data-stu-id="c6cdd-222">Browser source maps</span></span>

* <span data-ttu-id="c6cdd-223">ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-223">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="c6cdd-224">ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-224">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span>
* <span data-ttu-id="c6cdd-225">その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。</span><span class="sxs-lookup"><span data-stu-id="c6cdd-225">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span> <span data-ttu-id="c6cdd-226">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c6cdd-226">Troubleshoot</span></span>
