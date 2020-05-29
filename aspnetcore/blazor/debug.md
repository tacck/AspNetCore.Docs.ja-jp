---
title:'セキュリティで保護された ASP.NET Core Blazor WebAssembly' author: description:'Blazor アプリをデバッグする方法について説明します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a>ASP.NET Core Blazor WebAssembly をデバッグする

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssembly アプリは、Chromium ベースのブラウザー (Edge/Chrome) のブラウザー開発ツールを使用してデバッグできます。  または、Visual Studio または Visual Studio Code を使用してアプリをデバッグすることもできます。

利用可能なシナリオには以下が含まれます。

* ブレークポイントの設定と削除を行う。
* Visual Studio と Visual Studio Code (<kbd>F5</kbd> サポート) でデバッグ サポートを使用して、アプリを実行する。
* コードを使用して、シングルステップ (<kbd>F10</kbd>) を実行する。
* ブラウザーで <kbd>F8</kbd> を使用するか、Visual Studio または Visual Studio Code で <kbd>F5</kbd> を使用して、コードの実行を再開する。
* *Locals* の表示で、ローカル変数の値を観察する。
* 呼び出し履歴を表示する。これには、JavaScript から .NET への呼び出しチェーンと、.NET から JavaScript への呼び出しチェーンが含まれます。

現時点では、次の操作を行うことは*できません*。

* 配列を検査する。
* メンバーをポイントして検査する。
* マネージ コードに対してデバッグをステップ実行する。
* 値型の検査を完全にサポートする。
* 未処理の例外の発生時に中断する。
* アプリの起動中にブレークポイントにヒットする。
* サービス ワーカーを使用してアプリをデバッグする。

今後のリリースでは、引き続きデバッグエクス ペリエンスが向上します。

## <a name="prerequisites"></a>必須コンポーネント

デバッグには、次のいずれかのブラウザーが必要です。

* Microsoft Edge (バージョン 80 以降)
* Google Chrome (バージョン 70 以降)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Visual Studio と Visual Studio Code のデバッグを有効にする

既存の Blazor WebAssembly アプリのデバッグを有効にするには、スタートアップ プロジェクトの *launchSettings. json* ファイルを更新して、各起動プロファイルに次の `inspectUri` プロパティを含めます。

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

更新されると、*launchSettings. json* ファイルは次の例のようになります。

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri` プロパティ:

* アプリが Blazor WebAssembly アプリであることを IDE で検出できるようにします。
* スクリプト デバッグ インフラストラクチャに対して、Blazor のデバッグ プロキシを使用してブラウザーに接続するように指示します。

## <a name="visual-studio"></a>Visual Studio

Visual Studio で Blazor WebAssembly アプリをデバッグするには、次のようにします。

1. ASP.NET Core でホストされる新しい Blazor WebAssembly アプリを作成します。
1. <kbd>F5</kbd> キーを押して、デバッガーでアプリを実行します。
1. `IncrementCount` メソッドで *Counter.razor* にブレークポイントを設定します。
1. **[カウンター]** タブに移動し、ボタンを選択してブレークポイントをヒットします。

   ![デバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. [ローカル] ウィンドウの `currentCount` フィールドの値を確認します。

   ![[ローカル] を表示する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <kbd>F5</kbd> キーを押して、実行を続行します。

Blazor WebAssembly アプリのデバッグ中に、サーバー コードをデバッグすることもできます。

1. `OnInitializedAsync` の *FetchData.razor* ページにブレークポイントを設定します。
1. `Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。
1. **フェッチ データ** タブに移動し、サーバーへの HTTP 要求を発行する直前に `FetchData` コンポーネント内の最初のブレークポイントをヒットします。

   ![フェッチ データをデバッグする](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <kbd>F5</kbd> キーを押して実行を続行し、`WeatherForecastController` 内のサーバーでブレークポイントをヒットします。

   ![デバッグ サーバー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <kbd>F5</kbd> をもう一度押して実行を続行し、天気予報テーブルがレンダリングされたことを確認します。

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code で Blazor WebAssembly アプリをデバッグするには、次のようにします。
 
1. `debug.javascript.usePreview` が `true` に設定された状態で、[C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)と [JavaScript デバッガー (夜間)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 拡張機能をインストールします。

   ![拡張機能](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS プレビュー デバッガー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. デバッグが有効な状態で、既存の Blazor WebAssembly アプリを開きます。

   * デバッグを有効にするために追加の設定が必要であることを示す、次の通知が表示された場合は、適切な拡張機能がインストールされており、JavaScript プレビューのデバッグが有効になっていることを確認してから、ウィンドウを再度読み込みます。

     ![追加の設定が必要](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * 通知では、ビルドおよびデバッグのために必要なアセットをアプリに追加することが提案されます。 **[はい]** を選択します。

     ![必要なアセットを追加する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. デバッガーでアプリを起動するには、次の 2 段階の手順を実行します。

   1\. **最初に**、 **.NET Core Launch (Blazor スタンドアロン)** 起動構成を使用してアプリを起動します。

   2\. **アプリが開始したら**、**Chrome の .NET Core Debug Blazor Web Assembly** 起動構成 (Chrome が必要) を使用してブラウザーを起動します。 Chrome ではなく Edge を使用するには、 *.vscode/launch.json* の起動構成の `type` を `pwa-chrome` から `pwa-msedge` に変更します。

1. `Counter` コンポーネントの `IncrementCount` メソッドにブレークポイントを設定し、ボタンを選択してブレークポイントをヒットします。

   ![VS Code のデバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>ブラウザーでデバッグする

1. 開発環境でアプリのデバッグ ビルドを実行します。

1. <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd> キーを押します。

1. リモート デバッグが有効な状態で、ブラウザーを実行する必要があります。 リモート デバッグが無効になっている場合、 **[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** というエラー ページが生成されます。 エラー ページには、Blazor デバッグ プロキシがアプリに接続できるように、デバッグ ポートを開いた状態でブラウザーを実行するための手順が記載されています。 *すべてのブラウザー インスタンス*を閉じ、指示に従ってブラウザーを再起動します。

リモート デバッグが有効な状態でブラウザーを実行すると、デバッグ用のキーボード ショートカットによって新しいデバッガー タブが開きます。少しすると、 **[ソース]** タブに、アプリ内の .NET アセンブリの一覧が表示されます。 各アセンブリを展開して、デバッグに使用できる *.cs*/ *.razor* ソース ファイルを見つけます。 ブレークポイントを設定し、アプリのタブに戻ります。コードの実行時にブレークポイントにヒットします。 ブレークポイントにヒットした後、コード全体をステップ実行する (<kbd>F10</kbd>) か、コードの実行を普通に再開 (<kbd>F8</kbd>) します。

Blazor は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。 デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。 プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。

## <a name="browser-source-maps"></a>ブラウザー ソース マップ

ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。 ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。 その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。

## <a name="troubleshoot"></a>トラブルシューティング

エラーが発生している場合は、次のヒントが役立つことがあります。

**[デバッガー]** タブで、ブラウザー内の開発者ツールを開きます。 コンソールで `localStorage.clear()` を実行して、任意のブレークポイントを削除します。
