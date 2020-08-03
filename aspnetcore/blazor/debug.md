---
title: ASP.NET Core Blazor WebAssembly をデバッグする
author: guardrex
description: Blazor アプリをデバッグする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: b4199c3a99af5875c5d9a87f29f7c7e2758ffd71
ms.sourcegitcommit: 5a36758cca2861aeb10840093e46d273a6e6e91d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87303561"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="6e473-103">ASP.NET Core Blazor WebAssembly をデバッグする</span><span class="sxs-lookup"><span data-stu-id="6e473-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="6e473-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="6e473-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="6e473-105">Blazor WebAssembly アプリは、Chromium ベースのブラウザー (Edge/Chrome) のブラウザー開発ツールを使用してデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="6e473-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="6e473-106">または、Visual Studio または Visual Studio Code を使用してアプリをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="6e473-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="6e473-107">利用可能なシナリオには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6e473-107">Available scenarios include:</span></span>

* <span data-ttu-id="6e473-108">ブレークポイントの設定と削除を行う。</span><span class="sxs-lookup"><span data-stu-id="6e473-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="6e473-109">Visual Studio と Visual Studio Code (<kbd>F5</kbd> サポート) でデバッグ サポートを使用して、アプリを実行する。</span><span class="sxs-lookup"><span data-stu-id="6e473-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="6e473-110">コードを使用して、シングルステップ (<kbd>F10</kbd>) を実行する。</span><span class="sxs-lookup"><span data-stu-id="6e473-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="6e473-111">ブラウザーで <kbd>F8</kbd> を使用するか、Visual Studio または Visual Studio Code で <kbd>F5</kbd> を使用して、コードの実行を再開する。</span><span class="sxs-lookup"><span data-stu-id="6e473-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="6e473-112">*Locals* の表示で、ローカル変数の値を観察する。</span><span class="sxs-lookup"><span data-stu-id="6e473-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="6e473-113">呼び出し履歴を表示する。これには、JavaScript から .NET への呼び出しチェーンと、.NET から JavaScript への呼び出しチェーンが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6e473-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="6e473-114">現時点では、次の操作を行うことは*できません*。</span><span class="sxs-lookup"><span data-stu-id="6e473-114">For now, you *can't*:</span></span>

* <span data-ttu-id="6e473-115">未処理の例外の発生時に中断する。</span><span class="sxs-lookup"><span data-stu-id="6e473-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="6e473-116">アプリの起動中にブレークポイントにヒットする。</span><span class="sxs-lookup"><span data-stu-id="6e473-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="6e473-117">今後のリリースでは、引き続きデバッグエクス ペリエンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="6e473-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6e473-118">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6e473-118">Prerequisites</span></span>

<span data-ttu-id="6e473-119">デバッグには、次のいずれかのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="6e473-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="6e473-120">Google Chrome (バージョン 70 以降) (既定値)</span><span class="sxs-lookup"><span data-stu-id="6e473-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="6e473-121">Microsoft Edge (バージョン 80 以降)</span><span class="sxs-lookup"><span data-stu-id="6e473-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="6e473-122">Visual Studio と Visual Studio Code のデバッグを有効にする</span><span class="sxs-lookup"><span data-stu-id="6e473-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="6e473-123">既存の Blazor WebAssembly アプリのデバッグを有効にするには、スタートアップ プロジェクトの `launchSettings.json` ファイルを更新して、各起動プロファイルに次の `inspectUri` プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="6e473-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="6e473-124">更新されると、`launchSettings.json` ファイルは次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="6e473-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="6e473-125">`inspectUri` プロパティ:</span><span class="sxs-lookup"><span data-stu-id="6e473-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="6e473-126">アプリが Blazor WebAssembly アプリであることを IDE で検出できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6e473-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="6e473-127">スクリプト デバッグ インフラストラクチャに対して、Blazor のデバッグ プロキシを使用してブラウザーに接続するように指示します。</span><span class="sxs-lookup"><span data-stu-id="6e473-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="6e473-128">起動したブラウザー (`browserInspectUri`) の Websocket プロトコル (`wsProtocol`)、ホスト (`url.hostname`)、ポート (`url.port`)、およびインスペクター URI のプレースホルダー値は、フレームワークによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="6e473-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="6e473-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e473-129">Visual Studio</span></span>

<span data-ttu-id="6e473-130">Visual Studio で Blazor WebAssembly アプリをデバッグするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6e473-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="6e473-131">新しい ASP.NET Core でホストされる Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6e473-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="6e473-132"><kbd>F5</kbd> キーを押して、デバッガーでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="6e473-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="6e473-133">`Pages/Counter.razor` の `IncrementCount` メソッドにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="6e473-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="6e473-134">**`Counter`** タブに移動し、ボタンを選択してブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="6e473-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![デバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="6e473-136">[ローカル] ウィンドウの `currentCount` フィールドの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="6e473-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![[ローカル] を表示する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="6e473-138"><kbd>F5</kbd> キーを押して、実行を続行します。</span><span class="sxs-lookup"><span data-stu-id="6e473-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="6e473-139">Blazor WebAssembly アプリのデバッグ中に、サーバー コードをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="6e473-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="6e473-140">`Pages/FetchData.razor` ページの <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="6e473-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="6e473-141">`Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="6e473-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="6e473-142">**`Fetch Data`** タブに移動し、サーバーへの HTTP 要求を発行する直前に `FetchData` コンポーネント内の最初のブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="6e473-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![フェッチ データをデバッグする](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="6e473-144"><kbd>F5</kbd> キーを押して実行を続行し、`WeatherForecastController` 内のサーバーでブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="6e473-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![デバッグ サーバー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="6e473-146"><kbd>F5</kbd> をもう一度押して実行を続行し、天気予報テーブルがレンダリングされたことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e473-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="6e473-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6e473-147">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="6e473-148">スタンドアロン Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="6e473-148">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="6e473-149">VS Code でスタンドアロンの Blazor WebAssembly アプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="6e473-149">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="6e473-150">デバッグを有効にするために追加の設定が必要であることを示す、次の通知が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e473-150">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![追加の設定が必要](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="6e473-152">通知を受信した場合:</span><span class="sxs-lookup"><span data-stu-id="6e473-152">If you receive the notification:</span></span>

   * <span data-ttu-id="6e473-153">最新の [C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e473-153">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="6e473-154">インストールされている拡張機能を確認するには、メニュー バーから **[表示]**  >  **[拡張機能]** を開くか、**アクティビティ** サイド バーにある**拡張機能**アイコンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6e473-154">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="6e473-155">JavaScript のプレビュー デバッグが有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e473-155">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="6e473-156">メニュー バーから設定を開きます ( **[ファイル]**  >  **[ユーザー設定]**  >  **[設定]** )。</span><span class="sxs-lookup"><span data-stu-id="6e473-156">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="6e473-157">キーワード「`debug preview`」を使用して検索します。</span><span class="sxs-lookup"><span data-stu-id="6e473-157">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="6e473-158">検索結果で、 **[デバッグ] > [JavaScript:Use Preview]\(JavaScript: 使用プレビュー\)** のチェック ボックスがオンになっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e473-158">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span>
   * <span data-ttu-id="6e473-159">ウィンドウを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6e473-159">Reload the window.</span></span>

1. <span data-ttu-id="6e473-160"><kbd>F5</kbd> キーボード ショートカットまたはメニュー項目を使用してデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="6e473-160">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="6e473-161">プロンプトが表示されたら、 **[Blazor WebAssembly Debug]** オプションを選択してデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="6e473-161">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![使用可能なデバッグ オプションの一覧](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="6e473-163">スタンドアロン アプリが起動され、デバッグ ブラウザーが開きます。</span><span class="sxs-lookup"><span data-stu-id="6e473-163">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="6e473-164">`Counter` コンポーネントの `IncrementCount` メソッドにブレークポイントを設定し、ボタンを選択してブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="6e473-164">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code のデバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="6e473-166">ホストされた Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="6e473-166">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="6e473-167">VS Code で、ホストされた Blazor WebAssembly アプリのソリューション フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="6e473-167">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="6e473-168">プロジェクトの起動構成が設定されていない場合は、次の通知が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6e473-168">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="6e473-169">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6e473-169">Select **Yes**.</span></span>

   ![必要なアセットを追加する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="6e473-171">ウィンドウの上部にあるコマンド パレットで、ホストされているソリューション内の *Server* プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="6e473-171">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="6e473-172">`launch.json` ファイルが、デバッガーを起動するための起動構成を使用して生成されます。</span><span class="sxs-lookup"><span data-stu-id="6e473-172">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="6e473-173">既存のデバッグセッションにアタッチする</span><span class="sxs-lookup"><span data-stu-id="6e473-173">Attach to an existing debugging session</span></span>

<span data-ttu-id="6e473-174">実行中の Blazor アプリにアタッチするには、次のように構成された `launch.json` ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="6e473-174">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="6e473-175">デバッグ セッションへのアタッチは、スタンドアロン アプリでのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6e473-175">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="6e473-176">フルスタック デバッグを使用するには、VS Code からアプリを起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e473-176">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="6e473-177">起動構成のオプション</span><span class="sxs-lookup"><span data-stu-id="6e473-177">Launch configuration options</span></span>

<span data-ttu-id="6e473-178">`blazorwasm` デバッグの種類では、次の起動構成オプションがサポートされています (`.vscode/launch.json`)。</span><span class="sxs-lookup"><span data-stu-id="6e473-178">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="6e473-179">オプション</span><span class="sxs-lookup"><span data-stu-id="6e473-179">Option</span></span>    | <span data-ttu-id="6e473-180">説明</span><span class="sxs-lookup"><span data-stu-id="6e473-180">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="6e473-181">`launch` を使用して、Blazor WebAssembly アプリにデバッグ セッションを起動してアタッチするか、`attach` を使用して既に実行中のアプリにデバッグ セッションをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="6e473-181">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="6e473-182">デバッグ時にブラウザーで開く URL。</span><span class="sxs-lookup"><span data-stu-id="6e473-182">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="6e473-183">既定値は `https://localhost:5001` です。</span><span class="sxs-lookup"><span data-stu-id="6e473-183">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="6e473-184">デバッグ セッション用に起動するブラウザー。</span><span class="sxs-lookup"><span data-stu-id="6e473-184">The browser to launch for the debugging session.</span></span> <span data-ttu-id="6e473-185">`edge` または `chrome` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6e473-185">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="6e473-186">既定値は `chrome` です。</span><span class="sxs-lookup"><span data-stu-id="6e473-186">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="6e473-187">JS デバッガーからログを生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e473-187">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="6e473-188">ログを生成するには `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6e473-188">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="6e473-189">ホストされている Blazor WebAssembly アプリを起動およびデバッグする場合は、`true` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e473-189">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="6e473-190">Web サーバーの絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="6e473-190">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="6e473-191">アプリをサブルートから提供する場合は設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e473-191">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="6e473-192">デバッグ セッションがアタッチされるのを待機するミリ秒単位の時間。</span><span class="sxs-lookup"><span data-stu-id="6e473-192">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="6e473-193">既定値は 30,000 ミリ秒 (30 秒) です。</span><span class="sxs-lookup"><span data-stu-id="6e473-193">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="6e473-194">ホストされたアプリのサーバーを実行する実行可能ファイルへの参照。</span><span class="sxs-lookup"><span data-stu-id="6e473-194">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="6e473-195">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e473-195">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="6e473-196">アプリを起動する作業ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="6e473-196">The working directory to launch the app under.</span></span> <span data-ttu-id="6e473-197">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e473-197">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="6e473-198">起動されたプロセスに提供する環境変数。</span><span class="sxs-lookup"><span data-stu-id="6e473-198">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="6e473-199">`hosted` が `true`に設定されている場合にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="6e473-199">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="6e473-200">起動構成の例</span><span class="sxs-lookup"><span data-stu-id="6e473-200">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="6e473-201">スタンドアロン Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="6e473-201">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="6e473-202">指定された URL で実行中のアプリにアタッチする</span><span class="sxs-lookup"><span data-stu-id="6e473-202">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="6e473-203">Microsoft Edge でホストされている Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="6e473-203">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="6e473-204">ブラウザーの構成の既定値は Google Chrome です。</span><span class="sxs-lookup"><span data-stu-id="6e473-204">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="6e473-205">デバッグに Microsoft Edge を使用する場合は、`browser` を `edge` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6e473-205">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="6e473-206">Google Chrome を使用するには、`browser` オプションを設定しないか、オプションの値を `chrome` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6e473-206">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="6e473-207">前の例では、`MyHostedApp.Server.dll` は *Server* アプリのアセンブリです。</span><span class="sxs-lookup"><span data-stu-id="6e473-207">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="6e473-208">`.vscode` フォルダーは、`Client`、`Server`、および `Shared` フォルダーの隣にあるソリューションのフォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="6e473-208">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="6e473-209">ブラウザーでデバッグする</span><span class="sxs-lookup"><span data-stu-id="6e473-209">Debug in the browser</span></span>

1. <span data-ttu-id="6e473-210">開発環境でアプリのデバッグ ビルドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6e473-210">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="6e473-211">ブラウザーを起動し、アプリの URL (`https://localhost:5001` など) に移動します。</span><span class="sxs-lookup"><span data-stu-id="6e473-211">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="6e473-212">ブラウザーで <kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd> キーを押すことによって、リモート デバッグの開始を試みます。</span><span class="sxs-lookup"><span data-stu-id="6e473-212">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="6e473-213">ブラウザーはリモート デバッグが有効で実行されている必要があります。これは既定ではありません。</span><span class="sxs-lookup"><span data-stu-id="6e473-213">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="6e473-214">リモート デバッグが無効になっている場合、"**デバッグ可能なブラウザー タブが見つからない**" というエラー ページと、デバッグ ポートを開いた状態でブラウザーを起動する手順がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="6e473-214">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="6e473-215">ブラウザーの指示に従って、新しいブラウザー ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="6e473-215">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="6e473-216">前のブラウザー ウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="6e473-216">Close the previous browser window.</span></span>

1. <span data-ttu-id="6e473-217">リモート デバッグが有効な状態でブラウザーを実行すると、デバッグ用のキーボード ショートカット (<kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>) によって新しいデバッガー タブが開きます。</span><span class="sxs-lookup"><span data-stu-id="6e473-217">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="6e473-218">少しすると、 **[ソース]** タブに、`file://` ノード内にあるアプリの .NET アセンブリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6e473-218">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="6e473-219">コンポーネント コード (`.razor` ファイル) と C# コード ファイル (`.cs`) で、設定したブレークポイントがコードの実行時にヒットします。</span><span class="sxs-lookup"><span data-stu-id="6e473-219">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="6e473-220">ブレークポイントにヒットした後、コード全体をステップ実行する (<kbd>F10</kbd>) か、コードの実行を普通に再開 (<kbd>F8</kbd>) します。</span><span class="sxs-lookup"><span data-stu-id="6e473-220">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="6e473-221">Blazor は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。</span><span class="sxs-lookup"><span data-stu-id="6e473-221">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="6e473-222">デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。</span><span class="sxs-lookup"><span data-stu-id="6e473-222">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="6e473-223">プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。</span><span class="sxs-lookup"><span data-stu-id="6e473-223">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="6e473-224">ブラウザー ソース マップ</span><span class="sxs-lookup"><span data-stu-id="6e473-224">Browser source maps</span></span>

<span data-ttu-id="6e473-225">ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e473-225">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="6e473-226">ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。</span><span class="sxs-lookup"><span data-stu-id="6e473-226">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="6e473-227">その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。</span><span class="sxs-lookup"><span data-stu-id="6e473-227">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6e473-228">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6e473-228">Troubleshoot</span></span>

<span data-ttu-id="6e473-229">エラーが発生している場合は、次のヒントが役立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="6e473-229">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="6e473-230">**[デバッガー]** タブで、ブラウザー内の開発者ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="6e473-230">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="6e473-231">コンソールで `localStorage.clear()` を実行して、任意のブレークポイントを削除します。</span><span class="sxs-lookup"><span data-stu-id="6e473-231">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="6e473-232">ASP.NET Core HTTPS 開発証明書がインストールされ、信頼されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e473-232">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="6e473-233">詳細については、「<xref:security/enforcing-ssl#troubleshoot-certificate-problems>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e473-233">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="6e473-234">Visual Studio では、 **[ツール]**  >  **[オプション]**  >  **[デバッグ]**  >  **[全般]** で **[Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)] (ASP.NET (Chrome、Edge、IE) の JavaScript デバッグを有効にする)** オプションをオンにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e473-234">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="6e473-235">これは、Visual Studio の既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="6e473-235">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="6e473-236">デバッグが機能していない場合は、オプションがオンになっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e473-236">If debugging isn't working, confirm that the option is selected.</span></span>
