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
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor の概要

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

Blazor を始めるには、選択したツールのガイダンスに従います。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **ASP.NET および Web 開発**ワークロードと共に、最新バージョンの [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) をインストールする

1. 新しいプロジェクトを作成します。

1. **[Blazor アプリ]** を選択します。 **[次へ]** を選択します。

1. **[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。 **[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。 **[作成]** を選択します。

1. Blazor WebAssembly エクスペリエンスについては、 **[Blazor WebAssembly アプリ]** テンプレートを選択します。 Blazor Server エクスペリエンスについては、 **[Blazor Server アプリ]** テンプレートを選択します。 **[作成]** を選択します。

   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 最新バージョンの [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) をインストールします。 以前に SDK をインストールした場合、コマンド シェルでコマンドを実行し、インストールされているバージョンを判断できます。

   ```dotnetcli
   dotnet --version
   ```

1. [Visual Studio Code](https://code.visualstudio.com/) の最新版をインストールします。

1. `debug.javascript.usePreview` が `true` に設定された状態で、最新の [Visual Studio Code 拡張機能用の C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) と [JavaScript デバッガー (夜間)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 拡張機能をインストールします。

  VS Code UI を利用して `debug.javascript.usePreview` を `true` に設定するには、 **[ファイル]** 、 **[ユーザー設定]** 、 **[設定]** の順に開き、「`debug javascript use preview`」を検索します。 **[Node.js と Chrome にプレビュー段階の新しい JavaScript デバッガーを使用します]** のチェック ボックスを選択します。

1. Blazor WebAssembly エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Blazor Server エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。

1. Visual Studio Code で *WebApplication1* フォルダーを開きます。

1. IDE によって、プロジェクトをビルドおよびデバッグするためにアセットを追加するよう要求されます。 **[はい]** を選択します。

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. [Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。

1. **[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から**新しい**プロジェクトを作成します。

1. サイドバーで、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** の順に選択します。

   Blazor WebAssembly エクスペリエンスについては、 **[Blazor WebAssembly アプリ]** テンプレートを選択します。 Blazor Server エクスペリエンスについては、 **[Blazor Server アプリ]** テンプレートを選択します。 **[次へ]** を選択します。

   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。

1. 次の構成を確認します。

   * **[ターゲット フレームワーク]** が **[.NET Core 3.1]** に設定されている
   * **[認証]** が **[認証なし]** に設定されている
   
   **[次へ]** を選択します。

1. **[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。 **[作成]** を選択します。

1. *デバッガーを使用せずに*アプリを実行するには、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。 **[実行]**  > 、 **[デバッグの開始]** か [実行] (&#9654;) ボタンでアプリを実行すると、"*デバッガーなしで*" アプリが実行されます。

開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。 証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 最新バージョンの [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) をインストールします。 以前に SDK をインストールした場合、コマンド シェルでコマンドを実行し、インストールされているバージョンを判断できます。

   ```dotnetcli
   dotnet --version
   ```

1. Blazor WebAssembly エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Blazor Server エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。

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

コンポーネント パラメーターは、属性または [子コンテンツ](xref:blazor/components/index#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。 `Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。

* 属性 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) を使用して、`IncrementAmount` のためのパブリック プロパティを追加します。
* `currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

属性を使用して、`Index` コンポーネントの `<Counter>` 要素内に `IncrementAmount` を指定します。

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

アプリを実行します。 `Index` コンポーネントは、 **[クリックしてください]** ボタンが選択されるたびに 10 ずつインクリメントされる独自のカウンターを持っています。 `/counter` にある `Counter` コンポーネント (*Counter.razor*) は、引き続き 1 ずつインクリメントされます。

## <a name="next-steps"></a>次の手順

Blazor アプリを段階的に構築する:

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/templates>
* <xref:signalr/introduction>
