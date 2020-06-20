---
title: ASP.NET Core Blazor の概要
author: guardrex
description: 希望のツールで Blazor アプリを構築することで、Blazor の利用を開始してください。
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
uid: blazor/get-started
ms.openlocfilehash: c90c3bf7ccef420101c66fe04d579920209b066c
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102332"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="269cc-103">ASP.NET Core Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="269cc-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="269cc-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="269cc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="269cc-105">Blazor を始めるには、選択したツールのガイダンスに従います。</span><span class="sxs-lookup"><span data-stu-id="269cc-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="269cc-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="269cc-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="269cc-107">**ASP.NET および Web 開発**ワークロードと共に、最新バージョンの [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) をインストールする</span><span class="sxs-lookup"><span data-stu-id="269cc-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="269cc-108">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="269cc-108">Create a new project.</span></span>

1. <span data-ttu-id="269cc-109">**[Blazor アプリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-109">Select **Blazor App**.</span></span> <span data-ttu-id="269cc-110">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-110">Select **Next**.</span></span>

1. <span data-ttu-id="269cc-111">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="269cc-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="269cc-112">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="269cc-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="269cc-113">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-113">Select **Create**.</span></span>

1. <span data-ttu-id="269cc-114">Blazor WebAssembly エクスペリエンスについては、 **[Blazor WebAssembly アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="269cc-115">Blazor Server エクスペリエンスについては、 **[Blazor Server アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="269cc-116">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-116">Select **Create**.</span></span>

   <span data-ttu-id="269cc-117">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="269cc-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="269cc-118"><kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="269cc-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="269cc-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="269cc-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="269cc-120">最新バージョンの [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="269cc-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="269cc-121">以前に SDK をインストールした場合、コマンド シェルでコマンドを実行し、インストールされているバージョンを判断できます。</span><span class="sxs-lookup"><span data-stu-id="269cc-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="269cc-122">[Visual Studio Code](https://code.visualstudio.com/) の最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="269cc-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="269cc-123">`debug.javascript.usePreview` が `true` に設定された状態で、最新の [Visual Studio Code 拡張機能用の C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) と [JavaScript デバッガー (夜間)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="269cc-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="269cc-124">VS Code UI を利用して `debug.javascript.usePreview` を `true` に設定するには、 **[ファイル]** 、 **[ユーザー設定]** 、 **[設定]** の順に開き、「`debug javascript use preview`」を検索します。</span><span class="sxs-lookup"><span data-stu-id="269cc-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="269cc-125">**[Node.js と Chrome にプレビュー段階の新しい JavaScript デバッガーを使用します]** のチェック ボックスを選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="269cc-126">Blazor WebAssembly エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="269cc-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="269cc-127">Blazor Server エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="269cc-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="269cc-128">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="269cc-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="269cc-129">Visual Studio Code で *WebApplication1* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="269cc-129">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="269cc-130">IDE によって、プロジェクトをビルドおよびデバッグするためにアセットを追加するよう要求されます。</span><span class="sxs-lookup"><span data-stu-id="269cc-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="269cc-131">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-131">Select **Yes**.</span></span>

1. <span data-ttu-id="269cc-132"><kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="269cc-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="269cc-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="269cc-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="269cc-134">[Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="269cc-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="269cc-135">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から**新しい**プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="269cc-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="269cc-136">サイドバーで、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="269cc-137">Blazor WebAssembly エクスペリエンスについては、 **[Blazor WebAssembly アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="269cc-138">Blazor Server エクスペリエンスについては、 **[Blazor Server アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="269cc-139">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-139">Select **Next**.</span></span>

   <span data-ttu-id="269cc-140">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="269cc-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="269cc-141">次の構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="269cc-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="269cc-142">**[ターゲット フレームワーク]** が **[.NET Core 3.1]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="269cc-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="269cc-143">**[認証]** が **[認証なし]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="269cc-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="269cc-144">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-144">Select **Next**.</span></span>

1. <span data-ttu-id="269cc-145">**[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="269cc-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="269cc-146">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-146">Select **Create**.</span></span>

1. <span data-ttu-id="269cc-147">*デバッガーを使用せずに*アプリを実行するには、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="269cc-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="269cc-148">**[実行]**  > 、 **[デバッグの開始]** か [実行] (&#9654;) ボタンでアプリを実行すると、"*デバッガーなしで*" アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="269cc-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="269cc-149">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="269cc-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="269cc-150">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="269cc-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="269cc-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="269cc-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="269cc-152">最新バージョンの [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="269cc-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="269cc-153">以前に SDK をインストールした場合、コマンド シェルでコマンドを実行し、インストールされているバージョンを判断できます。</span><span class="sxs-lookup"><span data-stu-id="269cc-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="269cc-154">Blazor WebAssembly エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="269cc-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="269cc-155">Blazor Server エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="269cc-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="269cc-156">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="269cc-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="269cc-157">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="269cc-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="269cc-158">サイドバーのタブから複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="269cc-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="269cc-159">Home</span><span class="sxs-lookup"><span data-stu-id="269cc-159">Home</span></span>
* <span data-ttu-id="269cc-160">カウンター</span><span class="sxs-lookup"><span data-stu-id="269cc-160">Counter</span></span>
* <span data-ttu-id="269cc-161">Fetch data (データのフェッチ)</span><span class="sxs-lookup"><span data-stu-id="269cc-161">Fetch data</span></span>

<span data-ttu-id="269cc-162">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="269cc-162">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="269cc-163">Web ページでカウンターをインクリメントするには、通常、JavaScript を記述することが必要ですが、Blazor では C# を使用できます。</span><span class="sxs-lookup"><span data-stu-id="269cc-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="269cc-164">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="269cc-164">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="269cc-165">ブラウザーで `/counter` の要求があると、上部の `@page` ディレクティブで指定されているように、`Counter` コンポーネントによってそのコンテンツがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="269cc-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="269cc-166">コンポーネントは、レンダリング ツリーのメモリ内表現としてレンダリングされ、柔軟かつ効率的な方法で UI を更新するために利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="269cc-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="269cc-167">**[クリックしてください]** ボタンを選択するたびに:</span><span class="sxs-lookup"><span data-stu-id="269cc-167">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="269cc-168">`onclick` イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="269cc-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="269cc-169">`IncrementCount` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="269cc-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="269cc-170">`currentCount` がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="269cc-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="269cc-171">コンポーネントが再度レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="269cc-171">The component is rendered again.</span></span>

<span data-ttu-id="269cc-172">ランタイムで新しいコンテンツと前のコンテンツが比較され、変更されたコンテンツのみがドキュメント オブジェクト モデル (DOM) に適用されます。</span><span class="sxs-lookup"><span data-stu-id="269cc-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="269cc-173">HTML 構文を使用して、別のコンポーネントにコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="269cc-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="269cc-174">たとえば、`Index` コンポーネントに `<Counter />` 要素を追加することで、アプリのホームページに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="269cc-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="269cc-175">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="269cc-175">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="269cc-176">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="269cc-176">Run the app.</span></span> <span data-ttu-id="269cc-177">ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="269cc-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="269cc-178">コンポーネント パラメーターは、属性または [子コンテンツ](xref:blazor/components/index#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="269cc-178">Component parameters are specified using attributes or [child content](xref:blazor/components/index#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="269cc-179">`Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="269cc-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="269cc-180">属性 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) を使用して、`IncrementAmount` のためのパブリック プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="269cc-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="269cc-181">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="269cc-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="269cc-182">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="269cc-182">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="269cc-183">属性を使用して、`Index` コンポーネントの `<Counter>` 要素内に `IncrementAmount` を指定します。</span><span class="sxs-lookup"><span data-stu-id="269cc-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="269cc-184">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="269cc-184">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="269cc-185">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="269cc-185">Run the app.</span></span> <span data-ttu-id="269cc-186">`Index` コンポーネントは、 **[クリックしてください]** ボタンが選択されるたびに 10 ずつインクリメントされる独自のカウンターを持っています。</span><span class="sxs-lookup"><span data-stu-id="269cc-186">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="269cc-187">`/counter` にある `Counter` コンポーネント (*Counter.razor*) は、引き続き 1 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="269cc-187">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="269cc-188">次の手順</span><span class="sxs-lookup"><span data-stu-id="269cc-188">Next steps</span></span>

<span data-ttu-id="269cc-189">Blazor アプリを段階的に構築する:</span><span class="sxs-lookup"><span data-stu-id="269cc-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="269cc-190">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="269cc-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
