---
title: ASP.NET Core Blazor の概要
author: guardrex
description: 希望のツールで Blazor アプリを構築することで、Blazor の利用を開始してください。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 052a787fbe6411dbaa953f10fcd982dfbd41f1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769456"
---
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor の概要

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor の使用を開始するには、使用するツールに向けたガイダンスに従ってください。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Blazor サーバー アプリを作成するには、**ASP.NET および Web 開発**ワークロードと共に、最新バージョンの [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) をインストールします。

   Blazor サーバー アプリと Blazor WebAssembly アプリを作成するには、**ASP.NET および Web 開発**ワークロードと共に、[Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) の最新プレビューをインストールします。

   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。

1. 次のコマンドを実行して、Blazor WebAssembly プレビュー テンプレートをインストールします。

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. 新しいプロジェクトを作成します。

1. **[Blazor アプリ]** を選択します。 **[次へ]** を選択します。

1. **[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。 **[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。 **[作成]** を選択します。

1. Blazor WebAssembly エクスペリエンス (Visual Studio 16.6 Preview 2 以降) については、 **[Blazor WebAssembly アプリ]** テンプレートを選択します。 Blazor サーバー エクスペリエンス (Visual Studio 16.4 以降) については、 **[Blazor サーバー アプリ]** テンプレートを選択します。 **[作成]** を選択します。

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. [.NET Core 3.1 SDK をインストールします](https://dotnet.microsoft.com/download/dotnet-core/3.1)。

1. 必要に応じて、次のコマンドを実行して、[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) プレビュー テンプレートをインストールします。

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。

   > [!NOTE]
   > 3\.2 Preview Blazor WebAssembly テンプレートを使用するには、[.NET Core SDK バージョン 3.1.201 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)が**必要です**。 コマンド シェルで `dotnet --version` を実行して、インストールされている .NET Core SDK バージョンを確認します。

1. [Visual Studio Code](https://code.visualstudio.com/) のインストール。

1. `debug.javascript.usePreview` が `true` に設定された状態で、最新の [Visual Studio Code 拡張機能用の C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) と [JavaScript デバッガー (夜間)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 拡張機能をインストールします。

1. Blazor Server エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Blazor WebAssembly エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. Visual Studio Code で *WebApplication1* フォルダーを開きます。

1. IDE によって、プロジェクトをビルドおよびデバッグするためにアセットを追加するよう要求されます。 **[はい]** を選択します。

1. Blazor サーバーを使用し、Visual Studio Code デバッガーを使用してアプリを実行します。

   Blazor WebAssembly を使用し、 **.NET Core Launch (Blazor Standalone)** の起動構成を使用してアプリを起動します。次に、**Chrome の .NET Core Debug Blazor Web Assembly** の起動構成 (Chrome が必要) を使用してブラウザーを起動します。 詳細については、「<xref:blazor/debug#visual-studio-code>」を参照してください。

1. ブラウザーで、`https://localhost:5001` に移動します。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

Visual Studio for Mac では Blazor サーバーがサポートされています。 現時点では、Blazor WebAssembly はサポートされていません。 macOS で Blazor WebAssembly アプリを作成するには、 **[.NET Core CLI]** タブのガイダンスに従ってください。2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。

1. [Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。

1. **[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から**新しい**プロジェクトを作成します。

1. サイドバーで、 **[.NET Core]**  >  **[アプリ]** の順に選択します。

1. **[Blazor Server App] (Blazor Server アプリ)** テンプレートを選択します。 **[次へ]** を選択します。

1. 次の構成を確認します。

   * **[ターゲット フレームワーク]** が **[.NET Core 3.1]** に設定されている
   * **[認証]** が **[認証なし]** に設定されている
   
   **[次へ]** を選択します。

1. **[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。 **[作成]** を選択します。

1. *デバッガーを使用せずに*アプリを実行するには、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。 デバッグは、現時点ではサポートされていません。

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。 証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. [.NET Core 3.1 SDK をインストールします](https://dotnet.microsoft.com/download/dotnet-core/3.1)。

1. 必要に応じて、次のコマンドを実行して、Blazor WebAssembly プレビュー テンプレートをインストールします。

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。

   > [!NOTE]
   > 3\.2 Preview Blazor WebAssembly テンプレートを使用するには、[.NET Core SDK バージョン 3.1.201 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)が**必要です**。 コマンド シェルで `dotnet --version` を実行して、インストールされている .NET Core SDK バージョンを確認します。

1. Blazor Server エクスペリエンスについては、コマンド シェルで以下のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Blazor WebAssembly エクスペリエンスについては、コマンド シェルで以下のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. ブラウザーで、`https://localhost:5001` に移動します。

---

サイドバーのタブから複数のページを使用できます。

* Home
* カウンター
* Fetch data (データのフェッチ)

Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。 Web ページでカウンターをインクリメントするには、通常、JavaScript を記述することが必要ですが、Blazor では C# を使用できます。

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

ブラウザーで `/counter` の要求があると、上部の `@page` ディレクティブで指定されているように、`Counter` コンポーネントによってそのコンテンツがレンダリングされます。 コンポーネントは、レンダリング ツリーのメモリ内表現としてレンダリングされ、柔軟かつ効率的な方法で UI を更新するために利用できるようになります。

**[クリックしてください]** ボタンを選択するたびに:

* `onclick` イベントが発生します。
* `IncrementCount` メソッドが呼び出されます。
* `currentCount` がインクリメントされます。
* コンポーネントが再度レンダリングされます。

ランタイムで新しいコンテンツと前のコンテンツが比較され、変更されたコンテンツのみがドキュメント オブジェクト モデル (DOM) に適用されます。

HTML 構文を使用して、別のコンポーネントにコンポーネントを追加します。 たとえば、`Index` コンポーネントに `<Counter />` 要素を追加することで、アプリのホームページに `Counter` コンポーネントを追加します。

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

アプリを実行します。 ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。

コンポーネント パラメーターは、属性または [子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。 `Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。

* 属性 `[Parameter]` を使用して、`IncrementAmount` のためのパブリック プロパティを追加します。
* `currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

属性を使用して、`Index` コンポーネントの `<Counter>` 要素内に `IncrementAmount` を指定します。

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

アプリを実行します。 `Index` コンポーネントは、 **[クリックしてください]** ボタンが選択されるたびに 10 ずつインクリメントされる独自のカウンターを持っています。 `/counter` にある `Counter` コンポーネント (*Counter.razor*) は、引き続き 1 ずつインクリメントされます。

## <a name="next-steps"></a>次の手順

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/templates>
* <xref:signalr/introduction>
