---
title: ASP.NET Core Blazor WebAssembly をデバッグする
author: guardrex
description: Blazor アプリをデバッグする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: b8dd272d673e84b45a39272531385ebfd1d06175
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900987"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="0a1a3-103">ASP.NET Core Blazor WebAssembly をデバッグする</span><span class="sxs-lookup"><span data-stu-id="0a1a3-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="0a1a3-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="0a1a3-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="0a1a3-105">Blazor WebAssembly アプリは、Chromium ベースのブラウザー (Edge/Chrome) のブラウザー開発ツールを使用してデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="0a1a3-106">次の統合開発環境 (IDE) を使用して、アプリをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="0a1a3-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a1a3-107">Visual Studio</span></span>
* <span data-ttu-id="0a1a3-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0a1a3-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="0a1a3-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a1a3-109">Visual Studio Code</span></span>

<span data-ttu-id="0a1a3-110">利用可能なシナリオには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-110">Available scenarios include:</span></span>

* <span data-ttu-id="0a1a3-111">ブレークポイントの設定と削除を行う。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="0a1a3-112">IDE でデバッグ サポートを使用してアプリを実行する。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="0a1a3-113">コードを 1 ステップずつ実行する。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-113">Single-step through the code.</span></span>
* <span data-ttu-id="0a1a3-114">IDE でキーボード ショートカットを使用してコード実行を再開する。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="0a1a3-115">*[ローカル]* ウィンドウで、ローカル変数の値を観察する。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="0a1a3-116">JavaScript と .NET の間の呼び出しチェーンなど、呼び出し履歴を確認する。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="0a1a3-117">現時点では、次の操作を行うことは*できません*。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-117">For now, you *can't*:</span></span>

* <span data-ttu-id="0a1a3-118">未処理の例外の発生時に中断する。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="0a1a3-119">デバッグ プロキシが実行される前に、アプリの起動中にブレークポイントにヒットします。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="0a1a3-120">これには、`Program.Main` (`Program.cs`) のブレークポイントと、アプリから要求された最初のページによって読み込まれるコンポーネントの [`OnInitialized{Async}` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)のブレークポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0a1a3-121">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0a1a3-121">Prerequisites</span></span>

<span data-ttu-id="0a1a3-122">デバッグには、次のいずれかのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="0a1a3-123">Google Chrome (バージョン 70 以降) (既定値)</span><span class="sxs-lookup"><span data-stu-id="0a1a3-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="0a1a3-124">Microsoft Edge (バージョン 80 以降)</span><span class="sxs-lookup"><span data-stu-id="0a1a3-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="0a1a3-125">Visual Studio for Mac のバージョン 8.8 (ビルド 1532) 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="0a1a3-126">Visual Studio for Mac の最新リリースをインストールします。それには、 **[Visual Studio for Mac をダウンロードする]** ボタンを選択します ([Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/))。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="0a1a3-127">Visual Studio 内から "*プレビュー*" チャネルを選択します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="0a1a3-128">詳細については、「[Visual Studio for Mac のプレビュー バージョンをインストールする](/visualstudio/mac/install-preview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="0a1a3-129">現在、macOS での Apple Safari はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="0a1a3-130">デバッグの有効化</span><span class="sxs-lookup"><span data-stu-id="0a1a3-130">Enable debugging</span></span>

<span data-ttu-id="0a1a3-131">既存の Blazor WebAssembly アプリのデバッグを有効にするには、スタートアップ プロジェクトの `launchSettings.json` ファイルを更新して、各起動プロファイルに次の `inspectUri` プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-131">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="0a1a3-132">更新されると、`launchSettings.json` ファイルは次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="0a1a3-133">`inspectUri` プロパティ:</span><span class="sxs-lookup"><span data-stu-id="0a1a3-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="0a1a3-134">アプリが Blazor WebAssembly アプリであることを IDE で検出できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-134">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="0a1a3-135">スクリプト デバッグ インフラストラクチャに対して、Blazor のデバッグ プロキシを使用してブラウザーに接続するように指示します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-135">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="0a1a3-136">起動したブラウザー (`browserInspectUri`) の Websocket プロトコル (`wsProtocol`)、ホスト (`url.hostname`)、ポート (`url.port`)、およびインスペクター URI のプレースホルダー値は、フレームワークによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a1a3-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a1a3-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a1a3-138">Visual Studio で Blazor WebAssembly アプリをデバッグするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-138">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="0a1a3-139">新しい ASP.NET Core でホストされる Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-139">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="0a1a3-140"><kbd>F5</kbd> キーを押して、デバッガーでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="0a1a3-141">**デバッグなしの開始** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="0a1a3-142">アプリがデバッグ構成で実行されている場合、デバッグのオーバーヘッドによって常にパフォーマンスがわずかに低下します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="0a1a3-143">"`*Client*`" アプリで、`Pages/Counter.razor` の `currentCount++;` 行にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-143">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="0a1a3-144">ブラウザーで `Counter` ページに移動し、 **[Click me]** ボタンを選択して、ブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="0a1a3-145">Visual Studio の **[ローカル]** ウィンドウで、`currentCount` フィールドの値を調べます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="0a1a3-146"><kbd>F5</kbd> キーを押して、実行を続行します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="0a1a3-147">Blazor WebAssembly アプリをデバッグしている間に、サーバー コードをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-147">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="0a1a3-148">`Pages/FetchData.razor` ページの <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="0a1a3-149">`Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="0a1a3-150">`Fetch Data` ページに移動し、サーバーに HTTP 要求を発行する直前の、`FetchData` コンポーネント内の最初のブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="0a1a3-151"><kbd>F5</kbd> キーを押して実行を続け、サーバーの `WeatherForecastController` 内のブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="0a1a3-152"><kbd>F5</kbd> キーをもう一度押して実行を続け、天気予報テーブルがブラウザーにレンダリングされることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="0a1a3-153">デバッグ プロキシが実行状態になる前のアプリの起動中には、ブレークポイントで停止することは**ありません**。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="0a1a3-154">これには、`Program.Main` (`Program.cs`) のブレークポイントと、アプリから要求された最初のページによって読み込まれるコンポーネントの [`OnInitialized{Async}` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)のブレークポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="0a1a3-155">アプリが `/` 以外の[アプリ ベース パス](xref:blazor/host-and-deploy/index#app-base-path)でホストされている場合、`Properties/launchSettings.json` で次のプロパティを更新し、アプリのベース パスを反映させます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-155">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="0a1a3-156">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="0a1a3-156">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="0a1a3-157">各プロファイルの `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="0a1a3-157">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="0a1a3-158">前の設定のプレースホルダー:</span><span class="sxs-lookup"><span data-stu-id="0a1a3-158">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="0a1a3-159">`{INSECURE PORT}`:セキュリティで保護されていないポート。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-159">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="0a1a3-160">既定では、ランダムな値が提供されますが、カスタム ポートが許可されます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-160">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="0a1a3-161">`{APP BASE PATH}`:アプリのベース パス。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-161">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="0a1a3-162">`{SECURE PORT}`:セキュリティで保護されているポート。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-162">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="0a1a3-163">既定では、ランダムな値が提供されますが、カスタム ポートが許可されます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="0a1a3-164">`{PROFILE 1, 2, ... N}`:起動設定プロファイル。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-164">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="0a1a3-165">通常、既定では、1 つのアプリから複数のプロファイルが指定されます (たとえば、IIS Express 用のプロファイルと、Kestrel サーバーで使用されるプロジェクト プロファイル)。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-165">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="0a1a3-166">次の例では、アプリは `/OAT` でホストされており、アプリ ベース パスは `<base href="/OAT/">` として `wwwroot/index.html` に構成されています。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-166">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="0a1a3-167">Blazor WebAssembly アプリにカスタム アプリ ベース パスを使用する方法については、「<xref:blazor/host-and-deploy/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-167">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a1a3-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a1a3-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="0a1a3-169">スタンドアロン Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="0a1a3-169">Debug standalone Blazor WebAssembly</span></span></h2>

1. <span data-ttu-id="0a1a3-170">VS Code でスタンドアロンの Blazor WebAssembly アプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-170">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="0a1a3-171">デバッグを有効にするために追加の設定が必要であることを示す通知が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-171">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="0a1a3-172">Blazor WebAssembly アプリケーションをデバッグするには、追加のセットアップが必要です。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-172">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="0a1a3-173">通知を受信した場合:</span><span class="sxs-lookup"><span data-stu-id="0a1a3-173">If you receive the notification:</span></span>

   * <span data-ttu-id="0a1a3-174">最新の [C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-174">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="0a1a3-175">インストールされている拡張機能を確認するには、メニュー バーから **[表示]**  >  **[拡張機能]** を開くか、**アクティビティ** サイド バーにある**拡張機能**アイコンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-175">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="0a1a3-176">JavaScript のプレビュー デバッグが有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-176">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="0a1a3-177">メニュー バーから設定を開きます ( **[ファイル]**  >  **[ユーザー設定]**  >  **[設定]** )。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-177">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="0a1a3-178">キーワード「`debug preview`」を使用して検索します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-178">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="0a1a3-179">検索結果で、 **[デバッグ] > [JavaScript:Use Preview]\(JavaScript: 使用プレビュー\)** のチェック ボックスがオンになっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-179">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="0a1a3-180">プレビュー デバッグを有効にするオプションがない場合、VS Code の最新版にアップグレードするか、[JavaScript デバッガー拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code バージョン 1.46 以前) をインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-180">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="0a1a3-181">ウィンドウを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-181">Reload the window.</span></span>

1. <span data-ttu-id="0a1a3-182"><kbd>F5</kbd> キーボード ショートカットまたはメニュー項目を使用してデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-182">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="0a1a3-183">**デバッグなしの開始** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-183">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="0a1a3-184">アプリがデバッグ構成で実行されている場合、デバッグのオーバーヘッドによって常にパフォーマンスがわずかに低下します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-184">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="0a1a3-185">プロンプトが表示されたら、 **[Blazor WebAssembly Debug]** オプションを選択してデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-185">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="0a1a3-186">スタンドアロン アプリが起動され、デバッグ ブラウザーが開きます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-186">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="0a1a3-187">"`*Client*`" アプリで、`Pages/Counter.razor` の `currentCount++;` 行にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-187">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="0a1a3-188">ブラウザーで `Counter` ページに移動し、 **[Click me]** ボタンを選択して、ブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-188">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="0a1a3-189">デバッグ プロキシが実行状態になる前のアプリの起動中には、ブレークポイントで停止することは**ありません**。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-189">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="0a1a3-190">これには、`Program.Main` (`Program.cs`) のブレークポイントと、アプリから要求された最初のページによって読み込まれるコンポーネントの [`OnInitialized{Async}` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)のブレークポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-190">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="0a1a3-191">ホストされた Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="0a1a3-191">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="0a1a3-192">VS Code で、ホストされた Blazor WebAssembly アプリのソリューション フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-192">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="0a1a3-193">プロジェクトの起動構成が設定されていない場合は、次の通知が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-193">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="0a1a3-194">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-194">Select **Yes**.</span></span>

   > <span data-ttu-id="0a1a3-195">Required assets to build and debug are missing from '{APPLICATION NAME}'. (ビルドとデバッグに必要な資産が、'<アプリケーション名>' にありません。)</span><span class="sxs-lookup"><span data-stu-id="0a1a3-195">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="0a1a3-196">追加しますか?</span><span class="sxs-lookup"><span data-stu-id="0a1a3-196">Add them?</span></span>

1. <span data-ttu-id="0a1a3-197">ウィンドウの上部にあるコマンド パレットで、ホストされているソリューション内の *Server* プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-197">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="0a1a3-198">`launch.json` ファイルが、デバッガーを起動するための起動構成を使用して生成されます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-198">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="0a1a3-199">既存のデバッグセッションにアタッチする</span><span class="sxs-lookup"><span data-stu-id="0a1a3-199">Attach to an existing debugging session</span></span>

<span data-ttu-id="0a1a3-200">実行中の Blazor アプリにアタッチするには、次のように構成された `launch.json` ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-200">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="0a1a3-201">デバッグ セッションへのアタッチは、スタンドアロン アプリでのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-201">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="0a1a3-202">フルスタック デバッグを使用するには、VS Code からアプリを起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-202">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="0a1a3-203">起動構成のオプション</span><span class="sxs-lookup"><span data-stu-id="0a1a3-203">Launch configuration options</span></span>

<span data-ttu-id="0a1a3-204">`blazorwasm` デバッグの種類では、次の起動構成オプションがサポートされています (`.vscode/launch.json`)。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-204">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="0a1a3-205">オプション</span><span class="sxs-lookup"><span data-stu-id="0a1a3-205">Option</span></span>    | <span data-ttu-id="0a1a3-206">説明</span><span class="sxs-lookup"><span data-stu-id="0a1a3-206">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="0a1a3-207">`launch` を使用して、Blazor WebAssembly アプリにデバッグ セッションを起動してアタッチするか、`attach` を使用して既に実行中のアプリにデバッグ セッションをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-207">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="0a1a3-208">デバッグ時にブラウザーで開く URL。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-208">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="0a1a3-209">既定値は `https://localhost:5001` です。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-209">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="0a1a3-210">デバッグ セッション用に起動するブラウザー。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-210">The browser to launch for the debugging session.</span></span> <span data-ttu-id="0a1a3-211">`edge` または `chrome` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-211">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="0a1a3-212">既定値は `chrome` です。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-212">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="0a1a3-213">JS デバッガーからログを生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-213">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="0a1a3-214">ログを生成するには `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-214">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="0a1a3-215">ホストされている Blazor WebAssembly アプリを起動およびデバッグする場合は、`true` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-215">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="0a1a3-216">Web サーバーの絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-216">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="0a1a3-217">アプリをサブルートから提供する場合は設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-217">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="0a1a3-218">デバッグ セッションがアタッチされるのを待機するミリ秒単位の時間。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-218">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="0a1a3-219">既定値は 30,000 ミリ秒 (30 秒) です。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-219">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="0a1a3-220">ホストされたアプリのサーバーを実行する実行可能ファイルへの参照。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-220">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="0a1a3-221">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-221">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="0a1a3-222">アプリを起動する作業ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-222">The working directory to launch the app under.</span></span> <span data-ttu-id="0a1a3-223">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-223">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="0a1a3-224">起動されたプロセスに提供する環境変数。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-224">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="0a1a3-225">`hosted` が `true`に設定されている場合にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-225">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="0a1a3-226">起動構成の例</span><span class="sxs-lookup"><span data-stu-id="0a1a3-226">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="0a1a3-227">スタンドアロン Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="0a1a3-227">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="0a1a3-228">指定された URL で実行中のアプリにアタッチする</span><span class="sxs-lookup"><span data-stu-id="0a1a3-228">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="0a1a3-229">Microsoft Edge でホストされている Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="0a1a3-229">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="0a1a3-230">ブラウザーの構成の既定値は Google Chrome です。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-230">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="0a1a3-231">デバッグに Microsoft Edge を使用する場合は、`browser` を `edge` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-231">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="0a1a3-232">Google Chrome を使用するには、`browser` オプションを設定しないか、オプションの値を `chrome` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-232">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="0a1a3-233">前の例では、`MyHostedApp.Server.dll` は *Server* アプリのアセンブリです。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-233">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="0a1a3-234">`.vscode` フォルダーは、`Client`、`Server`、および `Shared` フォルダーの隣にあるソリューションのフォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-234">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a1a3-235">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0a1a3-235">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a1a3-236">Visual Studio for Mac で Blazor WebAssembly アプリをデバッグするには:</span><span class="sxs-lookup"><span data-stu-id="0a1a3-236">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="0a1a3-237">新しい ASP.NET Core でホストされる Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-237">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="0a1a3-238"><kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> キーを押して、デバッガーでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="0a1a3-239">**デバッグなしの開始** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-239">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="0a1a3-240">アプリがデバッグ構成で実行されている場合、デバッグのオーバーヘッドによって常にパフォーマンスがわずかに低下します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-240">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="0a1a3-241">デバッグ セッション用のブラウザーとしては、Google Chrome または Microsoft Edge を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-241">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="0a1a3-242">"`*Client*`" アプリで、`Pages/Counter.razor` の `currentCount++;` 行にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-242">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="0a1a3-243">ブラウザーで `Counter` ページに移動し、 **[Click me]** ボタンを選択して、ブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-243">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="0a1a3-244">Visual Studio の **[ローカル]** ウィンドウで、`currentCount` フィールドの値を調べます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-244">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="0a1a3-245"><kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> キーを押して、実行を続けます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-245">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="0a1a3-246">Blazor WebAssembly アプリをデバッグしている間に、サーバー コードをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-246">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="0a1a3-247">`Pages/FetchData.razor` ページの <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-247">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="0a1a3-248">`Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-248">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="0a1a3-249">`Fetch Data` ページに移動し、サーバーに HTTP 要求を発行する直前の、`FetchData` コンポーネント内の最初のブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-249">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="0a1a3-250"><kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> キーを押して実行を続け、サーバーの `WeatherForecastController` 内のブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-250">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="0a1a3-251"><kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> キーをもう一度押して実行を続け、天気予報テーブルがブラウザーにレンダリングされることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-251">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="0a1a3-252">デバッグ プロキシが実行状態になる前のアプリの起動中には、ブレークポイントで停止することは**ありません**。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-252">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="0a1a3-253">これには、`Program.Main` (`Program.cs`) のブレークポイントと、アプリから要求された最初のページによって読み込まれるコンポーネントの [`OnInitialized{Async}` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)のブレークポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-253">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="0a1a3-254">詳細については、「[Visual Studio for Mac を使用したデバッグ](/visualstudio/mac/debugging)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-254">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="0a1a3-255">ブラウザーでデバッグする</span><span class="sxs-lookup"><span data-stu-id="0a1a3-255">Debug in the browser</span></span>

<span data-ttu-id="0a1a3-256">*このセクションのガイダンスは、Windows で実行されている Google Chrome と Microsoft Edge に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="0a1a3-256">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="0a1a3-257">開発環境でアプリのデバッグ ビルドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-257">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="0a1a3-258">ブラウザーを起動し、アプリの URL (`https://localhost:5001` など) に移動します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-258">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="0a1a3-259">ブラウザーで <kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd> キーを押すことにより、リモート デバッグの開始を試みます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-259">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="0a1a3-260">ブラウザーはリモート デバッグが有効で実行されている必要があります。これは既定ではありません。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-260">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="0a1a3-261">リモート デバッグが無効になっている場合、"**デバッグ可能なブラウザー タブが見つからない**" というエラー ページと、デバッグ ポートを開いた状態でブラウザーを起動する手順がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-261">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="0a1a3-262">ブラウザーの指示に従って、新しいブラウザー ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-262">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="0a1a3-263">前のブラウザー ウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-263">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="0a1a3-264">リモート デバッグが有効な状態でブラウザーが実行されていると、前のステップのデバッグ キーボード ショートカットにより、新しいデバッガー タブが開きます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-264">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="0a1a3-265">少しすると、 **[ソース]** タブに、`file://` ノード内にあるアプリの .NET アセンブリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-265">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="0a1a3-266">コンポーネント コード (`.razor` ファイル) と C# コード ファイル (`.cs`) で、設定したブレークポイントがコードの実行時にヒットします。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-266">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="0a1a3-267">ブレークポイントにヒットした後、コード全体をステップ実行する (<kbd>F10</kbd>) か、コードの実行を普通に再開 (<kbd>F8</kbd>) します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-267">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="0a1a3-268">Blazor は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-268">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="0a1a3-269">デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-269">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="0a1a3-270">プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-270">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="0a1a3-271">ブラウザー ソース マップ</span><span class="sxs-lookup"><span data-stu-id="0a1a3-271">Browser source maps</span></span>

<span data-ttu-id="0a1a3-272">ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-272">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="0a1a3-273">ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-273">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="0a1a3-274">その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-274">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0a1a3-275">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="0a1a3-275">Troubleshoot</span></span>

<span data-ttu-id="0a1a3-276">エラーが発生している場合は、次のヒントが役立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-276">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="0a1a3-277">**[デバッガー]** タブで、ブラウザー内の開発者ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-277">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="0a1a3-278">コンソールで `localStorage.clear()` を実行して、任意のブレークポイントを削除します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-278">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="0a1a3-279">ASP.NET Core HTTPS 開発証明書がインストールされ、信頼されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-279">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="0a1a3-280">詳細については、「<xref:security/enforcing-ssl#troubleshoot-certificate-problems>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-280">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="0a1a3-281">Visual Studio では、 **[ツール]**  >  **[オプション]**  >  **[デバッグ]**  >  **[全般]** で **[Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)] (ASP.NET (Chrome、Edge、IE) の JavaScript デバッグを有効にする)** オプションをオンにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-281">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="0a1a3-282">これは、Visual Studio の既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-282">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="0a1a3-283">デバッグが機能していない場合は、オプションがオンになっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-283">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="0a1a3-284">ご利用の環境で HTTP プロキシを使用している場合は、プロキシ バイパス設定に `localhost` が含まれていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-284">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="0a1a3-285">これを行うには、次のいずれかに `NO_PROXY` 環境変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-285">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="0a1a3-286">プロジェクトの `launchSettings.json` ファイル。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-286">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="0a1a3-287">すべてのアプリに適用する場合は、ユーザーまたはシステム環境変数レベルで。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-287">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="0a1a3-288">環境変数を使用する場合は、Visual Studio を再起動して変更を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-288">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="0a1a3-289">`OnInitialized{Async}` 内のブレークポイントにヒットしない</span><span class="sxs-lookup"><span data-stu-id="0a1a3-289">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="0a1a3-290">Blazor フレームワークのデバッグ プロキシは、起動に少し時間がかかります。そのため、[`OnInitialized{Async}` ライフサイクル メソッド](xref:blazor/components/lifecycle#component-initialization-methods)内のブレークポイントにヒットしない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-290">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="0a1a3-291">デバッグ プロキシが起動してブレークポイントにヒットするまでの時間を確保するために、メソッド本体の開始に対して遅延を追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-291">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="0a1a3-292">[`if` コンパイラ ディレクティブ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)に基づいて遅延を含めると、アプリのリリース ビルドに対しては遅延が存在しないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-292">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="0a1a3-293"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="0a1a3-293"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="0a1a3-294"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="0a1a3-294"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="0a1a3-295">Visual Studio (Windows) のタイムアウト</span><span class="sxs-lookup"><span data-stu-id="0a1a3-295">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="0a1a3-296">Visual Studio で、タイムアウトに達したことを示すデバッグ アダプターの起動失敗の例外がスローされた場合、レジストリ設定でタイムアウトを調整できます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-296">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="0a1a3-297">上記のコマンドの `{TIMEOUT}` プレースホルダーはミリ秒単位です。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-297">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="0a1a3-298">たとえば、1 分は `60000` として割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="0a1a3-298">For example, one minute is assigned as `60000`.</span></span>
