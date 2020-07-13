---
title: ASP.NET Core Blazor 用のツール
author: guardrex
description: Blazor アプリのビルドに使用できるツールについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 30a76eda0e94ee7bb2b2d3db918bc029865bdf1a
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147650"
---
# <a name="tooling-for-aspnet-core-blazor"></a>ASP.NET Core Blazor 用のツール

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

::: zone pivot="os-windows"

1. **ASP.NET および Web 開発**ワークロードと共に、最新バージョンの [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) をインストールする

1. 新しいプロジェクトを作成します。

1. **[Blazor アプリ]** を選択します。 **[次へ]** を選択します。

1. **[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。 **[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。 **[作成]** を選択します。

1. Blazor WebAssembly エクスペリエンスの場合は、 **Blazor WebAssembly アプリ** テンプレートを選択します。 Blazor Server エクスペリエンスの場合は、 **Blazor Server アプリ** テンプレートを選択します。 **[作成]** を選択します。

   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。

ASP.NET Core HTTPS 開発証明書の信頼の詳細については、「<xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>」を参照してください。

::: zone-end

::: zone pivot="os-linux"

1. 最新バージョンの [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) をインストールします。 以前に SDK をインストールした場合、コマンド シェルでコマンドを実行し、インストールされているバージョンを判断できます。

   ```dotnetcli
   dotnet --version
   ```

1. [Visual Studio Code](https://code.visualstudio.com/) の最新版をインストールします。

1. 最新の [C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)をインストールします。

1. Blazor WebAssembly エクスペリエンスの場合は、コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Blazor Server エクスペリエンスの場合は、コマンド シェルで次のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。

1. Visual Studio Code で `WebApplication1` フォルダーを開きます。

1. IDE によって、プロジェクトをビルドおよびデバッグするためにアセットを追加するよう要求されます。 **[はい]** を選択します。

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。

## <a name="trust-a-development-certificate"></a>開発証明書を信頼する

Linux で証明書を信頼するための一元的な方法はありません。 通常、次のいずれかの方法が採用されます。

* ブラウザーの除外リストでアプリの URL を除外します。
* `localhost` に対するすべての自己署名証明書を信頼します。
* ブラウザーの信頼された証明書の一覧に証明書を追加します。

詳細については、ブラウザーおよび Linux ディストリビューションで提供されているガイダンスを参照してください。

::: zone-end

::: zone pivot="os-macos"

1. [Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。

1. **[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から**新しい**プロジェクトを作成します。

1. サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。

   Blazor WebAssembly エクスペリエンスの場合は、 **Blazor WebAssembly アプリ** テンプレートを選択します。 Blazor Server エクスペリエンスの場合は、 **Blazor Server アプリ** テンプレートを選択します。 **[次へ]** を選択します。

   2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。

1. **[認証]** に **[認証なし]** が設定されていることを確認します。 **[次へ]** を選択します。

1. **[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。 **[作成]** を選択します。

1. *デバッガーを使用せずに*アプリを実行するには、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。 **[実行]**  > 、 **[デバッグの開始]** か [実行] (&#9654;) ボタンでアプリを実行すると、"*デバッガーなしで*" アプリが実行されます。

開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。 証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。 ASP.NET Core HTTPS 開発証明書の信頼の詳細については、「<xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>」を参照してください。

::: zone-end
