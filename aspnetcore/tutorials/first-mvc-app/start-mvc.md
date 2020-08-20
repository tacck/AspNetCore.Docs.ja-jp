---
title: ASP.NET Core MVC の概要
author: rick-anderson
description: ASP.NET Core MVC の概要について説明します。
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 9d70b292a93a5d19cc25b2fc592ec88ce8262434
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629991"
---
# <a name="get-started-with-aspnet-core-mvc"></a>ASP.NET Core MVC の概要

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。

このアプリでは、映画タイトルのデータベースを管理します。 以下の方法について説明します。

> [!div class="checklist"]
> * Web アプリを作成する。
> * モデルを追加してスキャフォールディングする。
> * データベースを使用する。
> * 検索と検証を追加する。

最後には、映画データを管理および表示できるアプリが完成します。

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>必須コンポーネント

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Web アプリの作成

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio から **[新しいプロジェクトの作成]** を選択します。

* **[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。 コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)

* **[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。

![[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web ](start-mvc/_static/new_project30.png)

Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。 プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。 これは基本的なスターター プロジェクトです。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

このチュートリアルは VS Code の知識があることを前提としています。 詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。

* [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。
* ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。
* 次のコマンドを実行します。

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * "**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、  **[はい]** を選択します

  * `dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。
  * `code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **[ファイル]** > **[新しいソリューション]** の順に選択します。

  ![macOS の新しいソリューション](start-mvc/_static/new_project_vsmac.png)

* バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。 バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。

  ![macOS Web アプリ テンプレートの選択](start-mvc/_static/web_app_template_vsmac.png)

* **[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。

  * **[認証]** に **[認証なし]** が設定されていることを確認します。
  * **[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 3.x バージョンを選択します。

  **[次へ]** を選択します。

* プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。

  ![macOS でプロジェクトに名前を付ける](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>アプリを実行する

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。 アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。
* Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。 多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。
* **[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* **[IIS Express]** ボタンを選択することで、アプリをデバッグできます。

  ![IIS Express](start-mvc/_static/iis_express.png)

  次の図はアプリを示しています。

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ctrl + F5 キーを押して、デバッガーなしで実行します。

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。 アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 localhost では、ローカル コンピューターからの Web 要求のみが処理されます。

  Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。 多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。 Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。

[!INCLUDE[](~/includes/trustCertMac.md)]

* アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。 アプリを実行する際には、別のポート番号が表示されます。
* **[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。

  次の図はアプリを示しています。

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。

> [!div class="step-by-step"]
> [次へ](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。

このアプリでは、映画タイトルのデータベースを管理します。 以下の方法について説明します。

> [!div class="checklist"]
> * Web アプリを作成する。
> * モデルを追加してスキャフォールディングする。
> * データベースを使用する。
> * 検索と検証を追加する。

最後には、映画データを管理および表示できるアプリが完成します。

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>必須コンポーネント

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Web アプリの作成

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio から **[新しいプロジェクトの作成]** を選択します。

* **[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。 コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)


* **[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。

![[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web ](start-mvc/_static/new_project22-21.png)

Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。 プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。 これは基本的なスターター プロジェクトなので、ここから始めることをお勧めします。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

このチュートリアルは VS Code の知識があることを前提としています。 詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。

* [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。
* ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。
* 次のコマンドを実行します。

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * "**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、  **[はい]** を選択します

  * `dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。
  * `code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **[ファイル]** > **[新しいソリューション]** の順に選択します。

  ![macOS の新しいソリューション](./start-mvc/_static/new_project_vsmac.png)

* バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。 バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。

* **[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。

  * **[認証]** に **[認証なし]** が設定されていることを確認します。
  * **[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 2.x バージョンを選択します。

  **[次へ]** を選択します。

* プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。

---

### <a name="run-the-app"></a>アプリを実行する

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。 アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。
* Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。 多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。
* **[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* **[IIS Express]** ボタンを選択することで、アプリをデバッグできます。

  ![IIS Express](start-mvc/_static/iis_express.png)

* **[同意する]** を選択し、追跡に同意します。 このアプリでは個人情報は追跡されません。 テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  次の図は、追跡に同意した後のアプリを示しています。

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ctrl + F5 キーを押して、デバッガーなしで実行します。

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。 アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 localhost では、ローカル コンピューターからの Web 要求のみが処理されます。

  Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。 多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。

* **[同意する]** を選択し、追跡に同意します。 このアプリでは個人情報は追跡されません。 テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  次の図は、追跡に同意した後のアプリを示しています。

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。 Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。

[!INCLUDE[](~/includes/trustCertMac.md)]

* アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。 アプリを実行する際には、別のポート番号が表示されます。
* **[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。

* **[同意する]** を選択し、追跡に同意します。 このアプリでは個人情報は追跡されません。 テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_privacy_macos.png)

  次の図は、追跡に同意した後のアプリを示しています。

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。

> [!div class="step-by-step"]
> [次へ](adding-controller.md)

::: moniker-end
