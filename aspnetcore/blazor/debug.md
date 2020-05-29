---
<span data-ttu-id="c77ed-101">title:'セキュリティで保護された ASP.NET Core Blazor WebAssembly' author: description:'Blazor アプリをデバッグする方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="c77ed-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="c77ed-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c77ed-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c77ed-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c77ed-103">'Blazor'</span></span>
- <span data-ttu-id="c77ed-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c77ed-104">'Identity'</span></span>
- <span data-ttu-id="c77ed-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c77ed-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c77ed-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c77ed-106">'Razor'</span></span>
- <span data-ttu-id="c77ed-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c77ed-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="c77ed-108">ASP.NET Core Blazor WebAssembly をデバッグする</span><span class="sxs-lookup"><span data-stu-id="c77ed-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="c77ed-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="c77ed-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="c77ed-110"> WebAssembly アプリは、Chromium ベースのブラウザー (Edge/Chrome) のブラウザー開発ツールを使用してデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="c77ed-111">または、Visual Studio または Visual Studio Code を使用してアプリをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="c77ed-112">利用可能なシナリオには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-112">Available scenarios include:</span></span>

* <span data-ttu-id="c77ed-113">ブレークポイントの設定と削除を行う。</span><span class="sxs-lookup"><span data-stu-id="c77ed-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="c77ed-114">Visual Studio と Visual Studio Code (<kbd>F5</kbd> サポート) でデバッグ サポートを使用して、アプリを実行する。</span><span class="sxs-lookup"><span data-stu-id="c77ed-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="c77ed-115">コードを使用して、シングルステップ (<kbd>F10</kbd>) を実行する。</span><span class="sxs-lookup"><span data-stu-id="c77ed-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="c77ed-116">ブラウザーで <kbd>F8</kbd> を使用するか、Visual Studio または Visual Studio Code で <kbd>F5</kbd> を使用して、コードの実行を再開する。</span><span class="sxs-lookup"><span data-stu-id="c77ed-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="c77ed-117">*Locals* の表示で、ローカル変数の値を観察する。</span><span class="sxs-lookup"><span data-stu-id="c77ed-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="c77ed-118">呼び出し履歴を表示する。これには、JavaScript から .NET への呼び出しチェーンと、.NET から JavaScript への呼び出しチェーンが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="c77ed-119">現時点では、次の操作を行うことは*できません*。</span><span class="sxs-lookup"><span data-stu-id="c77ed-119">For now, you *can't*:</span></span>

* <span data-ttu-id="c77ed-120">未処理の例外の発生時に中断する。</span><span class="sxs-lookup"><span data-stu-id="c77ed-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="c77ed-121">アプリの起動中にブレークポイントにヒットする。</span><span class="sxs-lookup"><span data-stu-id="c77ed-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="c77ed-122">今後のリリースでは、引き続きデバッグエクス ペリエンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c77ed-123">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c77ed-123">Prerequisites</span></span>

<span data-ttu-id="c77ed-124">デバッグには、次のいずれかのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="c77ed-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="c77ed-125">Microsoft Edge (バージョン 80 以降)</span><span class="sxs-lookup"><span data-stu-id="c77ed-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="c77ed-126">Google Chrome (バージョン 70 以降)</span><span class="sxs-lookup"><span data-stu-id="c77ed-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="c77ed-127">Visual Studio と Visual Studio Code のデバッグを有効にする</span><span class="sxs-lookup"><span data-stu-id="c77ed-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="c77ed-128">既存の Blazor WebAssembly アプリのデバッグを有効にするには、スタートアップ プロジェクトの *launchSettings. json* ファイルを更新して、各起動プロファイルに次の `inspectUri` プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="c77ed-129">更新されると、*launchSettings. json* ファイルは次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c77ed-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="c77ed-130">`inspectUri` プロパティ:</span><span class="sxs-lookup"><span data-stu-id="c77ed-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="c77ed-131">アプリが Blazor WebAssembly アプリであることを IDE で検出できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c77ed-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="c77ed-132">スクリプト デバッグ インフラストラクチャに対して、Blazor のデバッグ プロキシを使用してブラウザーに接続するように指示します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="c77ed-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c77ed-133">Visual Studio</span></span>

<span data-ttu-id="c77ed-134">Visual Studio で Blazor WebAssembly アプリをデバッグするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c77ed-134">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="c77ed-135">ASP.NET Core でホストされる新しい Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-135">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="c77ed-136"><kbd>F5</kbd> キーを押して、デバッガーでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-136">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="c77ed-137">`IncrementCount` メソッドで *Counter.razor* にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-137">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="c77ed-138">**[カウンター]** タブに移動し、ボタンを選択してブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="c77ed-138">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![デバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="c77ed-140">[ローカル] ウィンドウの `currentCount` フィールドの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-140">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![[ローカル] を表示する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="c77ed-142"><kbd>F5</kbd> キーを押して、実行を続行します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-142">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="c77ed-143">Blazor WebAssembly アプリのデバッグ中に、サーバー コードをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-143">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="c77ed-144"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> の *FetchData.razor* ページにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-144">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="c77ed-145">`Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-145">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="c77ed-146">**フェッチ データ** タブに移動し、サーバーへの HTTP 要求を発行する直前に `FetchData` コンポーネント内の最初のブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="c77ed-146">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![フェッチ データをデバッグする](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="c77ed-148"><kbd>F5</kbd> キーを押して実行を続行し、`WeatherForecastController` 内のサーバーでブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="c77ed-148">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![デバッグ サーバー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="c77ed-150"><kbd>F5</kbd> をもう一度押して実行を続行し、天気予報テーブルがレンダリングされたことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-150">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="c77ed-151">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c77ed-151">Visual Studio Code</span></span>

<span data-ttu-id="c77ed-152">Visual Studio Code で Blazor WebAssembly アプリをデバッグするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c77ed-152">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="c77ed-153">`debug.javascript.usePreview` が `true` に設定された状態で、[C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)と [JavaScript デバッガー (夜間)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="c77ed-153">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![拡張機能](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS プレビュー デバッガー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="c77ed-156">デバッグが有効な状態で、既存の Blazor WebAssembly アプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-156">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="c77ed-157">デバッグを有効にするために追加の設定が必要であることを示す、次の通知が表示された場合は、適切な拡張機能がインストールされており、JavaScript プレビューのデバッグが有効になっていることを確認してから、ウィンドウを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-157">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![追加の設定が必要](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="c77ed-159">通知では、ビルドおよびデバッグのために必要なアセットをアプリに追加することが提案されます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-159">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="c77ed-160">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-160">Select **Yes**:</span></span>

     ![必要なアセットを追加する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="c77ed-162">デバッガーでアプリを起動するには、次の 2 段階の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-162">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="c77ed-163">1\.</span><span class="sxs-lookup"><span data-stu-id="c77ed-163">1\.</span></span> <span data-ttu-id="c77ed-164">**最初に**、 **.NET Core Launch (Blazor スタンドアロン)** 起動構成を使用してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-164">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="c77ed-165">2\.</span><span class="sxs-lookup"><span data-stu-id="c77ed-165">2\.</span></span> <span data-ttu-id="c77ed-166">**アプリが開始したら**、**Chrome の .NET Core Debug Blazor Web Assembly** 起動構成 (Chrome が必要) を使用してブラウザーを起動します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-166">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="c77ed-167">Chrome ではなく Edge を使用するには、 *.vscode/launch.json* の起動構成の `type` を `pwa-chrome` から `pwa-msedge` に変更します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-167">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="c77ed-168">`Counter` コンポーネントの `IncrementCount` メソッドにブレークポイントを設定し、ボタンを選択してブレークポイントをヒットします。</span><span class="sxs-lookup"><span data-stu-id="c77ed-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code のデバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="c77ed-170">ブラウザーでデバッグする</span><span class="sxs-lookup"><span data-stu-id="c77ed-170">Debug in the browser</span></span>

1. <span data-ttu-id="c77ed-171">開発環境でアプリのデバッグ ビルドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-171">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="c77ed-172"><kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd> キーを押します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-172">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="c77ed-173">リモート デバッグが有効な状態で、ブラウザーを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c77ed-173">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="c77ed-174">リモート デバッグが無効になっている場合、 **[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** というエラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-174">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="c77ed-175">エラー ページには、Blazor デバッグ プロキシがアプリに接続できるように、デバッグ ポートを開いた状態でブラウザーを実行するための手順が記載されています。</span><span class="sxs-lookup"><span data-stu-id="c77ed-175">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="c77ed-176">*すべてのブラウザー インスタンス*を閉じ、指示に従ってブラウザーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-176">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="c77ed-177">リモート デバッグが有効な状態でブラウザーを実行すると、デバッグ用のキーボード ショートカットによって新しいデバッガー タブが開きます。少しすると、 **[ソース]** タブに、アプリ内の .NET アセンブリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-177">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="c77ed-178">各アセンブリを展開して、デバッグに使用できる *.cs*/ *.razor* ソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-178">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="c77ed-179">ブレークポイントを設定し、アプリのタブに戻ります。コードの実行時にブレークポイントにヒットします。</span><span class="sxs-lookup"><span data-stu-id="c77ed-179">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="c77ed-180">ブレークポイントにヒットした後、コード全体をステップ実行する (<kbd>F10</kbd>) か、コードの実行を普通に再開 (<kbd>F8</kbd>) します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-180">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="c77ed-181"> は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。</span><span class="sxs-lookup"><span data-stu-id="c77ed-181"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="c77ed-182">デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-182">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="c77ed-183">プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。</span><span class="sxs-lookup"><span data-stu-id="c77ed-183">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="c77ed-184">ブラウザー ソース マップ</span><span class="sxs-lookup"><span data-stu-id="c77ed-184">Browser source maps</span></span>

<span data-ttu-id="c77ed-185">ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-185">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="c77ed-186">ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。</span><span class="sxs-lookup"><span data-stu-id="c77ed-186">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="c77ed-187">その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。</span><span class="sxs-lookup"><span data-stu-id="c77ed-187">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c77ed-188">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c77ed-188">Troubleshoot</span></span>

<span data-ttu-id="c77ed-189">エラーが発生している場合は、次のヒントが役立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="c77ed-189">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="c77ed-190">**[デバッガー]** タブで、ブラウザー内の開発者ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="c77ed-190">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="c77ed-191">コンソールで `localStorage.clear()` を実行して、任意のブレークポイントを削除します。</span><span class="sxs-lookup"><span data-stu-id="c77ed-191">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
