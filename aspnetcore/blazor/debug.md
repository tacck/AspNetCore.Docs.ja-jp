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
# <a name="debug-aspnet-core-blazor-webassembly"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor'

'SignalR' uid: blazor/debug

* ASP.NET Core Blazor WebAssembly をデバッグする
* [Daniel Roth](https://github.com/danroth27)
* Blazor WebAssembly アプリは、Chromium ベースのブラウザー (Edge/Chrome) のブラウザー開発ツールを使用してデバッグできます。
* または、Visual Studio または Visual Studio Code を使用してアプリをデバッグすることもできます。
* 利用可能なシナリオには以下が含まれます。
* ブレークポイントの設定と削除を行う。

Visual Studio と Visual Studio Code (<kbd>F5</kbd> サポート) でデバッグ サポートを使用して、アプリを実行する。

* コードを使用して、シングルステップ (<kbd>F10</kbd>) を実行する。
* ブラウザーで <kbd>F8</kbd> を使用するか、Visual Studio または Visual Studio Code で <kbd>F5</kbd> を使用して、コードの実行を再開する。

*Locals* の表示で、ローカル変数の値を観察する。

## <a name="prerequisites"></a>呼び出し履歴を表示する。これには、JavaScript から .NET への呼び出しチェーンと、.NET から JavaScript への呼び出しチェーンが含まれます。

現時点では、次の操作を行うことは*できません*。

* 未処理の例外の発生時に中断する。
* アプリの起動中にブレークポイントにヒットする。

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>今後のリリースでは、引き続きデバッグエクス ペリエンスが向上します。

必須コンポーネント

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

デバッグには、次のいずれかのブラウザーが必要です。

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Microsoft Edge (バージョン 80 以降)

* Google Chrome (バージョン 70 以降)
* Visual Studio と Visual Studio Code のデバッグを有効にする

既存の Blazor WebAssembly アプリのデバッグを有効にするには、スタートアップ プロジェクトの *launchSettings. json* ファイルを更新して、各起動プロファイルに次の `inspectUri` プロパティを含めます。

## <a name="visual-studio"></a>更新されると、*launchSettings. json* ファイルは次の例のようになります。

`inspectUri` プロパティ:

1. アプリが Blazor WebAssembly アプリであることを IDE で検出できるようにします。
1. スクリプト デバッグ インフラストラクチャに対して、Blazor のデバッグ プロキシを使用してブラウザーに接続するように指示します。
1. 起動したブラウザー (`browserInspectUri`) の Websocket プロトコル (`wsProtocol`)、ホスト (`url.hostname`)、ポート (`url.port`)、およびインスペクター URI のプレースホルダー値は、フレームワークによって提供されます。
1. Visual Studio

   ![Visual Studio で Blazor WebAssembly アプリをデバッグするには、次のようにします。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. ASP.NET Core でホストされる新しい Blazor WebAssembly アプリを作成します。

   ![<kbd>F5</kbd> キーを押して、デバッガーでアプリを実行します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. `IncrementCount` メソッドで *Counter.razor* にブレークポイントを設定します。

**[カウンター]** タブに移動し、ボタンを選択してブレークポイントをヒットします。

1. デバッグ カウンター
1. [ローカル] ウィンドウの `currentCount` フィールドの値を確認します。
1. [ローカル] を表示する

   ![<kbd>F5</kbd> キーを押して、実行を続行します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Blazor WebAssembly アプリのデバッグ中に、サーバー コードをデバッグすることもできます。

   ![<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> の *FetchData.razor* ページにブレークポイントを設定します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. `Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。

<a id="vscode"></a>

## <a name="visual-studio-code"></a>**フェッチ データ** タブに移動し、サーバーへの HTTP 要求を発行する直前に `FetchData` コンポーネント内の最初のブレークポイントをヒットします。

フェッチ データをデバッグする
 
<kbd>F5</kbd> キーを押して実行を続行し、`WeatherForecastController` 内のサーバーでブレークポイントをヒットします。

![デバッグ サーバー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![<kbd>F5</kbd> をもう一度押して実行を続行し、天気予報テーブルがレンダリングされたことを確認します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Visual Studio Code

1. Visual Studio Code で Blazor WebAssembly アプリをデバッグするには、次のようにします。

   `debug.javascript.usePreview` が `true` に設定された状態で、[C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)と [JavaScript デバッガー (夜間)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 拡張機能をインストールします。
   
   * 拡張機能
   * JS プレビュー デバッガー
   * スタンドアロンの Blazor WebAssembly のデバッグ

   ![VS Code でスタンドアロンの Blazor WebAssembly アプリを開きます。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. デバッグを有効にするために追加の設定が必要であるという次の通知が表示された場合は、以下のようにします。

1. 正しい拡張機能がインストールされていることを確認します。

   ![JavaScript のプレビュー デバッグが有効になっていることを確認します。](index/_static/blazor-vscode-debugtypes.png)

1. ウィンドウを再度読み込みます。

1. 追加の設定が必要

   ![<kbd>F5</kbd> キーボード ショートカットまたはメニュー項目を使用してデバッグを開始します。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>プロンプトが表示されたら、 **[Blazor WebAssembly Debug]** オプションを選択してデバッグを開始します。

1. 使用可能なデバッグ オプションの一覧

1. スタンドアロン アプリが起動され、デバッグ ブラウザーが開きます。 `Counter` コンポーネントの `IncrementCount` メソッドにブレークポイントを設定し、ボタンを選択してブレークポイントをヒットします。

   ![VS Code のデバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. ホストされた Blazor WebAssembly のデバッグ

ホストされた Blazor WebAssembly アプリを VS Code で開きます。

### <a name="attach-to-an-existing-debugging-session"></a>プロジェクトの起動構成が設定されていない場合は、次の通知が表示されます。

**[はい]** を選択します。

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> 必要なアセットを追加する 選択ウィンドウで、ホストされているソリューション内の *Server* プロジェクトを選択します。

### <a name="launch-configuration-options"></a>*launch.json* ファイルが、デバッガーを起動するための起動構成を使用して生成されます。

既存のデバッグセッションにアタッチする

| 実行中の Blazor アプリにアタッチするには、次のように構成された *launch.json* ファイルを作成します。    | デバッグ セッションへのアタッチは、スタンドアロン アプリでのみサポートされています。 |
| --------- | ----------- |
| `request` | フルスタック デバッグを使用するには、VS Code からアプリを起動する必要があります。 |
| `url`     | 起動構成のオプション `blazorwasm` デバッグの種類では、次の起動構成オプションがサポートされています。 |
| `browser` | オプション 説明 `launch` を使用して、Blazor WebAssembly アプリにデバッグ セッションを起動してアタッチするか、`attach` を使用して既に実行中のアプリにデバッグセッションをアタッチします。 |
| `trace`   | デバッグ時にブラウザーで開く URL。 既定値は `https://localhost:5001` です。 |
| `hosted`  | デバッグ セッション用に起動するブラウザー。 |
| `webRoot` | `edge` または `chrome` に設定します。 既定値は `chrome` です。 |
| `timeout` | JS デバッガーからログを生成するために使用されます。 ログを生成するには `true` に設定します。 |
| `program` | ホストされている Blazor WebAssembly アプリを起動およびデバッグする場合は、`true` に設定する必要があります。 Web サーバーの絶対パスを指定します。 |
| `cwd`     | アプリをサブルートから提供する場合は設定する必要があります。 デバッグ セッションがアタッチされるのを待機するミリ秒単位の時間。 |
| `env`     | 既定値は 30,000 ミリ秒 (30 秒) です。 ホストされたアプリのサーバーを実行する実行可能ファイルへの参照。 |

### <a name="example-launch-configurations"></a>`hosted` が `true` の場合に設定する必要があります。

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>アプリを起動する作業ディレクトリ。

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>`hosted` が `true` の場合に設定する必要があります。

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>起動されたプロセスに提供する環境変数。

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>`hosted` が `true`に設定されている場合にのみ適用されます。

1. 起動構成の例

1. スタンドアロン Blazor WebAssembly アプリを起動してデバッグする

1. 指定された URL で実行中のアプリにアタッチする ホストされている Blazor WebAssembly アプリを起動してデバッグする ブラウザーでデバッグする 開発環境でアプリのデバッグ ビルドを実行します。

<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd> キーを押します。 リモート デバッグが有効な状態で、ブラウザーを実行する必要があります。 リモート デバッグが無効になっている場合、 **[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** というエラー ページが生成されます。 エラー ページには、Blazor デバッグ プロキシがアプリに接続できるように、デバッグ ポートを開いた状態でブラウザーを実行するための手順が記載されています。

*すべてのブラウザー インスタンス*を閉じ、指示に従ってブラウザーを再起動します。 リモート デバッグが有効な状態でブラウザーを実行すると、デバッグ用のキーボード ショートカットによって新しいデバッガー タブが開きます。少しすると、 **[ソース]** タブに、アプリ内の .NET アセンブリの一覧が表示されます。 各アセンブリを展開して、デバッグに使用できる *.cs*/ *.razor* ソース ファイルを見つけます。

## <a name="browser-source-maps"></a>ブレークポイントを設定し、アプリのタブに戻ります。コードの実行時にブレークポイントにヒットします。

ブレークポイントにヒットした後、コード全体をステップ実行する (<kbd>F10</kbd>) か、コードの実行を普通に再開 (<kbd>F8</kbd>) します。 Blazor は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。 デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。

## <a name="troubleshoot"></a>プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。

ブラウザー ソース マップ

* ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。 ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。
* その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。 トラブルシューティング
