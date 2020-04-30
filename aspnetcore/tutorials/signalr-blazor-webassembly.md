---
title: Blazor WebAssembly で ASP.NET Core SignalR を使用する
author: guardrex
description: Blazor WebAssembly で ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 78c5fbb8b91b934bcb34525672e9e26b6a95290e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111150"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Blazor WebAssembly で ASP.NET Core SignalR を使用する

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

このチュートリアルでは、Blazor WebAssembly で SignalR を使用してリアルタイム アプリを構築するための基本について説明します。 以下の方法について説明します。

> [!div class="checklist"]
> * Blazor WebAssembly でホストされるアプリ プロジェクトを作成する
> * SignalR クライアント ライブラリを追加する
> * SignalR ハブを追加する
> * SignalR サービスと SignalR ハブのエンドポイントを追加する
> * チャット用の Razor コンポーネント コードを追加する

このチュートリアルの最後には、動作するチャット アプリが完成します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>必須コンポーネント

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>ホストされる Blazor WebAssembly アプリ プロジェクトを作成する

Visual Studio バージョン 16.6 Preview 2 以降を使用していない場合は、[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) テンプレートをインストールします。 Blazor WebAssembly がプレビュー段階にある間、[Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) パッケージにはプレビュー バージョンが用意されています。 コマンド シェルで次のコマンドを実行します。

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
```

使用するツールに向けたガイダンスに従ってください。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 新しいプロジェクトを作成します。

1. **[Blazor アプリ]** を選択し、 **[次へ]** を選択します。

1. **[プロジェクト名]** フィールドに「BlazorSignalRApp」と入力します。 **[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。 **[作成]** を選択します。

1. **[Blazor WebAssembly アプリ]** テンプレートを選択します。

1. **[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。

1. **[作成]** を選択します。

> [!NOTE]
> 新しいバージョンの Visual Studio にアップグレードした場合、またはインストールした場合で、VS の UI に Blazor WebAssembly テンプレートが表示されないときは、前述の `dotnet new` コマンドを使用してテンプレートを再インストールしてください。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。

1. アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。 Visual Studio Code によって、生成された *launch.json* および *tasks.json* ファイルを含む *.vscode* フォルダーが自動的に追加されます。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Visual Studio for Mac で、プロジェクト フォルダーに移動してプロジェクトのソリューション ファイル ( *.sln*) を開くことで、プロジェクトを開きます。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

コマンド シェルで次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>SignalR クライアント ライブラリを追加する

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **ソリューション エクスプローラー**で、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が *nuget.org* に設定されていることを確認します。

1. **[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。

1. 検索結果から `Microsoft.AspNetCore.SignalR.Client` パッケージを選択し、 **[インストール]** を選択します。

1. **[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。

1. **[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. **[ソリューション]** サイド バーで、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが *nuget.org* に設定されていることを確認します。

1. **[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。

1. 検索結果の `Microsoft.AspNetCore.SignalR.Client` パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。

1. **[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

コマンド シェルで、次のコマンドを実行します。

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>SignalR ハブを追加する

**BlazorSignalRApp.Server** プロジェクトで、*Hubs* (複数形) フォルダーを作成し、次の `ChatHub` クラスを追加します (*Hubs/ChatHub.cs*)。

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>SignalR ハブのサービスとエンドポイントを追加する

1. **BlazorSignalRApp.Server** プロジェクトで、*Startup.cs* ファイルを開きます。

1. ファイルの先頭に `ChatHub` クラスの名前空間を追加します。

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. `Startup.Configure` で、コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>チャット用の Razor コンポーネント コードを追加する

1. **BlazorSignalRApp.Client** プロジェクトで、*Pages/Index.razor* ファイルを開きます。

1. マークアップを次のコードで置き換えます。

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>アプリを実行する

1. お使いのツール用のガイダンスに従ってください。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **ソリューション エクスプローラー**で、**BlazorSignalRApp.Server** プロジェクトを選択します。 <kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。

1. アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。

1. いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。 両方のページに、その名前とメッセージが瞬時に表示されます。

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. VS Code により、サーバー アプリの起動プロファイルの作成が提案される場合 ( *.vscode/launch.json*)、`program` エントリが次のように表示され、アプリのアセンブリをポイントします (`{APPLICATION NAME}.Server.dll`)。

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。

1. アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。

1. いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。 両方のページに、その名前とメッセージが瞬時に表示されます。

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. **[ソリューション]** サイド バーで、**BlazorSignalRApp.Server** プロジェクトを選択します。 <kbd>⌘</kbd>+<kbd>↩</kbd>* * を押して、デバッグしてアプリを実行するか、<kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグなしでアプリを実行します。

1. アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。

1. いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。 両方のページに、その名前とメッセージが瞬時に表示されます。

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. コマンド シェルで、次のコマンドを実行します。

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。

1. いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。 両方のページに、その名前とメッセージが瞬時に表示されます。

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * Blazor WebAssembly でホストされるアプリ プロジェクトを作成する
> * SignalR クライアント ライブラリを追加する
> * SignalR ハブを追加する
> * SignalR サービスと SignalR ハブのエンドポイントを追加する
> * チャット用の Razor コンポーネント コードを追加する

Blazor アプリの構築について詳しくは、Blazor のドキュメントを参照してください。

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>その他の技術情報

* <xref:signalr/introduction>
