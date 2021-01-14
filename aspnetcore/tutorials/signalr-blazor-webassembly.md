---
title: ホストされた Blazor WebAssembly アプリで ASP.NET Core SignalR を使用する
author: guardrex
description: Blazor WebAssembly で ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
no-loc:
- appsettings.json
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 2f5630eac65b880bdefff2a4baf4f1878e981536
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058390"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a>ホストされた Blazor WebAssembly アプリで ASP.NET Core SignalR を使用する

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

このチュートリアルでは、Blazor WebAssembly と共に SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。 以下の方法について説明します。

> [!div class="checklist"]
> * Blazor WebAssembly でホストされるアプリ プロジェクトを作成する
> * SignalR クライアント ライブラリを追加する
> * SignalR ハブを追加する
> * SignalR サービスと SignalR ハブのエンドポイントを追加する
> * チャット用の Razor コンポーネント コードを追加する

このチュートリアルの最後には、動作するチャット アプリが完成します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>必須コンポーネント

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.8 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [Visual Studio for Mac バージョン 8.8 以降](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [Visual Studio for Mac バージョン 8.6 以降](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>ホストされる Blazor WebAssembly アプリ プロジェクトを作成する

使用するツールに向けたガイダンスに従ってください。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16.8 以降と .NET Core SDK 5.0.0 以降が必要です。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16.6 以降と .NET Core SDK 3.1.300 以降が必要です。

::: moniker-end

1. 新しいプロジェクトを作成します。

1. **[Blazor アプリ]** を選択し、 **[次へ]** を選択します

1. **[プロジェクト名]** フィールドに「`BlazorSignalRApp`」と入力します。 **[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。 **[作成]** を選択します。

1. **Blazor WebAssembly アプリ** テンプレートを選択します。

1. **[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。

1. **[作成]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。

1. アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。 Visual Studio Code では、生成された `launch.json` ファイルと `tasks.json` ファイルが含まれる `.vscode` フォルダーが自動的に追加されます。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 最新バージョンの [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) をインストールし、次の手順を実行します。

1. **[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から **新しい** プロジェクトを作成します。

1. サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。

1. **Blazor WebAssembly アプリ** テンプレートを選択します。 **[次へ]** を選択します。

1. **[認証]** に **[認証なし]** が設定されていることを確認します。 **[ASP.NET Core Hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。 **[次へ]** を選択します。

1. **[プロジェクト名]** フィールドで、アプリに `BlazorSignalRApp` という名前を付けます。 **[作成]** を選択します。

   開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。 証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。

1. プロジェクト フォルダーに移動し、プロジェクトのソリューション ファイル (`.sln`) を開いてプロジェクトを開きます。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

コマンド シェルで次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>SignalR クライアント ライブラリを追加する

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **ソリューション エクスプローラー** で、`BlazorSignalRApp.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。

1. **[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。

1. 検索結果から [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージを選択し、 **[インストール]** を選択します。

1. **[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。

1. **[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. **[ソリューション]** サイドバーで、`BlazorSignalRApp.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。

1. **[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。

1. 検索結果の [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。

1. **[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

コマンド シェルで、次のコマンドを実行します。

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>System.Text.Encodings.Web パッケージを追加する

ASP.NET Core 3.1 アプリで [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 を使用する場合のパッケージの解決に関する問題のため、`BlazorSignalRApp.Server` プロジェクトで [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) のパッケージ参照が必要になります。 基になる問題は、.NET 5 の今後のパッチ リリースで解決される予定です。 詳細については、「[System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560)」を参照してください。

ASP.NET Core 3.1 でホストされている Blazor ソリューションの `BlazorSignalRApp.Server` プロジェクトに [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) を追加するには、使用するツールのガイダンスに従ってください。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **ソリューション エクスプローラー** で、`BlazorSignalRApp.Server` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。

1. **[参照]** が選択されている状態で、検索ボックスに「`System.Text.Encodings.Web`」と入力します。

1. 検索結果から [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) パッケージを選択し、 **[インストール]** を選択します。

1. **[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。

1. **[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**統合ターミナル** (ツール バーの **[表示]** > **[ターミナル]** ) で、次のコマンドを実行します。

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. **[ソリューション]** サイドバーで、`BlazorSignalRApp.Server` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。

1. **[参照]** が選択されている状態で、検索ボックスに「`System.Text.Encodings.Web`」と入力します。

1. 検索結果の [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。

1. **[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

コマンド シェルで次のコマンドを実行します。

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a>SignalR ハブを追加する

`BlazorSignalRApp.Server` プロジェクトで、`Hubs` (複数形) フォルダーを作成し、次の `ChatHub` クラス (`Hubs/ChatHub.cs`) を追加します。

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>サービスと SignalR ハブのエンドポイントを追加する

1. `BlazorSignalRApp.Server` プロジェクトで、`Startup.cs` ファイルを開きます。

1. ファイルの先頭に `ChatHub` クラスの名前空間を追加します。

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. `Startup.Configure`の場合:

   * 処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。
   * コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. `Startup.Configure`の場合:

   * 処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。
   * コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>チャット用の Razor コンポーネント コードを追加する

1. `BlazorSignalRApp.Client` プロジェクトで、`Pages/Index.razor` ファイルを開きます。

::: moniker range=">= aspnetcore-5.0"

1. マークアップを次のコードで置き換えます。

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. マークアップを次のコードで置き換えます。

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>アプリを実行する

お使いのツール用のガイダンスに従ってください。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **ソリューション エクスプローラー** で `BlazorSignalRApp.Server` プロジェクトを選択します。 <kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。

1. アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。

1. いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。 両方のページに、その名前とメッセージが瞬時に表示されます。

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. <kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。

1. アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。

1. いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。 両方のページに、その名前とメッセージが瞬時に表示されます。

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. **[ソリューション]** サイドバーで、`BlazorSignalRApp.Server` プロジェクトを選択します。 <kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグしてアプリを実行するか、<kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグなしでアプリを実行します。

1. アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。

1. いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。 両方のページに、その名前とメッセージが瞬時に表示されます。

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. コマンド シェルで、次のコマンドを実行します。

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。

1. いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。 両方のページに、その名前とメッセージが瞬時に表示されます。

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
> [Identity Server、WebSockets、Server-Sent Events によるベアラー トークン認証](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>その他の技術情報

* <xref:signalr/introduction>
* [認証のための SignalR のクロスオリジンネゴシエーション](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
