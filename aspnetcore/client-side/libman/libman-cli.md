---
title: ASP.NET Core で LibMan CLI を使用する
author: scottaddie
description: ASP.NET Core プロジェクトで LibMan CLI を使用する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 1a42d162e28d4bb4cce284b8b5e37f1be6ff64c6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770553"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="6830b-103">ASP.NET Core で LibMan CLI を使用する</span><span class="sxs-lookup"><span data-stu-id="6830b-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="6830b-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6830b-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6830b-105">[LibMan](xref:client-side/libman/index) CLI は、.NET Core がサポートされているすべての場所でサポートされているクロスプラットフォーム ツールです。</span><span class="sxs-lookup"><span data-stu-id="6830b-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6830b-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6830b-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="6830b-107">インストール</span><span class="sxs-lookup"><span data-stu-id="6830b-107">Installation</span></span>

<span data-ttu-id="6830b-108">LibMan CLI をインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="6830b-109">[.NET Core グローバル ツール](/dotnet/core/tools/global-tools#install-a-global-tool)は、[Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet パッケージからインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6830b-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="6830b-110">特定の NuGet パッケージ ソースから LibMan CLI をインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="6830b-111">この例では、ローカル Windows コンピューターの *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* ファイルから .NET Core グローバル ツールがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6830b-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="6830b-112">使用方法</span><span class="sxs-lookup"><span data-stu-id="6830b-112">Usage</span></span>

<span data-ttu-id="6830b-113">CLI のインストールが正常に完了したら、次のコマンドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6830b-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="6830b-114">インストールされている CLI のバージョンを表示するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="6830b-115">使用可能な CLI コマンドを表示するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="6830b-116">上のコマンドを実行すると、次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-116">The preceding command displays output similar to the following:</span></span>

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

<span data-ttu-id="6830b-117">次のセクションでは、使用可能な CLI コマンドの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="6830b-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="6830b-118">プロジェクト内の LibMan の初期化</span><span class="sxs-lookup"><span data-stu-id="6830b-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="6830b-119">`libman init` コマンドを実行すると、*libman.json* ファイルが存在しない場合は作成されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="6830b-120">このファイルは、既定の項目テンプレート コンテンツを使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6830b-121">構文</span><span class="sxs-lookup"><span data-stu-id="6830b-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6830b-122">オプション</span><span class="sxs-lookup"><span data-stu-id="6830b-122">Options</span></span>

<span data-ttu-id="6830b-123">`libman init` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6830b-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="6830b-124">現在のフォルダーを基準とした相対パス。</span><span class="sxs-lookup"><span data-stu-id="6830b-124">A path relative to the current folder.</span></span> <span data-ttu-id="6830b-125">*libman.json* のライブラリに `destination` プロパティが定義されていない場合、ライブラリ ファイルはこの場所にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6830b-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="6830b-126">`<PATH>` 値は *libman.json* の `defaultDestination` プロパティに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="6830b-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="6830b-127">指定されたライブラリでプロバイダーが定義されていない場合に使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="6830b-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="6830b-128">`<PROVIDER>` 値は *libman.json* の `defaultProvider` プロパティに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="6830b-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="6830b-129">`<PROVIDER>` を次のいずれかの値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6830b-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6830b-130">使用例</span><span class="sxs-lookup"><span data-stu-id="6830b-130">Examples</span></span>

<span data-ttu-id="6830b-131">ASP.NET Core プロジェクトで *libman.json* ファイルを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="6830b-132">プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="6830b-132">Navigate to the project root.</span></span>
* <span data-ttu-id="6830b-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6830b-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="6830b-134">既定のプロバイダーの名前を入力するか、`Enter` を押して既定の CDNJS プロバイダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="6830b-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="6830b-135">有効な値を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6830b-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init コマンド - 既定のプロバイダー](_static/libman-init-provider.png)

<span data-ttu-id="6830b-137">次の内容の *libman.json* ファイルがプロジェクト ルートに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="6830b-138">ライブラリ ファイルの追加</span><span class="sxs-lookup"><span data-stu-id="6830b-138">Add library files</span></span>

<span data-ttu-id="6830b-139">`libman install` コマンドを実行すると、ライブラリ ファイルがダウンロードされ、プロジェクトにインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6830b-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="6830b-140">*libman.json* ファイルが存在しない場合は追加されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="6830b-141">*libman.json* ファイルは、ライブラリ ファイルの構成の詳細を格納するように変更されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6830b-142">構文</span><span class="sxs-lookup"><span data-stu-id="6830b-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6830b-143">引数</span><span class="sxs-lookup"><span data-stu-id="6830b-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6830b-144">インストールするライブラリの名前。</span><span class="sxs-lookup"><span data-stu-id="6830b-144">The name of the library to install.</span></span> <span data-ttu-id="6830b-145">この名前には、バージョン番号の表記 (`@1.2.0` など) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="6830b-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="6830b-146">オプション</span><span class="sxs-lookup"><span data-stu-id="6830b-146">Options</span></span>

<span data-ttu-id="6830b-147">`libman install` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6830b-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="6830b-148">ライブラリをインストールする場所。</span><span class="sxs-lookup"><span data-stu-id="6830b-148">The location to install the library.</span></span> <span data-ttu-id="6830b-149">指定しない場合は、既定の場所が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-149">If not specified, the default location is used.</span></span> <span data-ttu-id="6830b-150">*libman.json* で `defaultDestination` プロパティが指定されていない場合、このオプションは必須です。</span><span class="sxs-lookup"><span data-stu-id="6830b-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="6830b-151">ライブラリからインストールするファイルの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="6830b-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="6830b-152">指定しない場合、ライブラリのすべてのファイルがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6830b-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="6830b-153">インストールするファイルごとに 1 つの `--files` オプションを指定します。</span><span class="sxs-lookup"><span data-stu-id="6830b-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="6830b-154">相対パスもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6830b-154">Relative paths are supported too.</span></span> <span data-ttu-id="6830b-155">たとえば、`--files dist/browser/signalr.js` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="6830b-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="6830b-156">ライブラリの取得に使用するプロバイダーの名前。</span><span class="sxs-lookup"><span data-stu-id="6830b-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="6830b-157">`<PROVIDER>` を次のいずれかの値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6830b-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="6830b-158">指定しない場合、*libman.json* の `defaultProvider` プロパティが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="6830b-159">*libman.json* で `defaultProvider` プロパティが指定されていない場合、このオプションは必須です。</span><span class="sxs-lookup"><span data-stu-id="6830b-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6830b-160">使用例</span><span class="sxs-lookup"><span data-stu-id="6830b-160">Examples</span></span>

<span data-ttu-id="6830b-161">次の *libman.json* ファイルについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="6830b-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="6830b-162">CDNJS プロバイダーを使用して JQuery バージョン 3.2.1 の *jquery.min.js* ファイルを *wwwroot/scripts/jquery* フォルダーにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="6830b-163">*libman.json* ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6830b-163">The *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

<span data-ttu-id="6830b-164">ファイル システム プロバイダーを使用して *C:\\temp\\contosoCalendar\\* から *calendar.js* ファイルと *calendar.css* ファイルをインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="6830b-165">次のプロンプトは、以下の 2 つの理由で表示されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="6830b-166">*libman.json* ファイルに `defaultDestination` プロパティが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="6830b-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="6830b-167">`libman install` コマンドに `-d|--destination` オプションが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="6830b-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman install コマンド - インストール先](_static/libman-install-destination.png)

<span data-ttu-id="6830b-169">既定の保存先を受け入れると、*libman.json* ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6830b-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a><span data-ttu-id="6830b-170">ライブラリ ファイルの復元</span><span class="sxs-lookup"><span data-stu-id="6830b-170">Restore library files</span></span>

<span data-ttu-id="6830b-171">`libman restore` コマンドは、*libman.json*で定義されているライブラリ ファイルをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6830b-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="6830b-172">次の規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-172">The following rules apply:</span></span>

* <span data-ttu-id="6830b-173">プロジェクト ルートに *libman.json* ファイルが存在しない場合は、エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="6830b-174">ライブラリがプロバイダーを指定している場合、*libman.json* の `defaultProvider` プロパティは無視されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="6830b-175">ライブラリがインストール先を指定している場合、*libman.json* の `defaultDestination` プロパティは無視されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6830b-176">構文</span><span class="sxs-lookup"><span data-stu-id="6830b-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6830b-177">オプション</span><span class="sxs-lookup"><span data-stu-id="6830b-177">Options</span></span>

<span data-ttu-id="6830b-178">`libman restore` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6830b-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6830b-179">使用例</span><span class="sxs-lookup"><span data-stu-id="6830b-179">Examples</span></span>

<span data-ttu-id="6830b-180">*libman.json* で定義されているライブラリ ファイルを復元するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="6830b-181">ライブラリ ファイルの削除</span><span class="sxs-lookup"><span data-stu-id="6830b-181">Delete library files</span></span>

<span data-ttu-id="6830b-182">`libman clean` コマンドは、LibMan を使用して以前に復元されたライブラリ ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="6830b-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="6830b-183">この操作の後、空になったフォルダーがあれば削除されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="6830b-184">*libman.json* の `libraries` プロパティでライブラリ ファイルに関連付けられている構成は削除されません。</span><span class="sxs-lookup"><span data-stu-id="6830b-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6830b-185">構文</span><span class="sxs-lookup"><span data-stu-id="6830b-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6830b-186">オプション</span><span class="sxs-lookup"><span data-stu-id="6830b-186">Options</span></span>

<span data-ttu-id="6830b-187">`libman clean` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6830b-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6830b-188">使用例</span><span class="sxs-lookup"><span data-stu-id="6830b-188">Examples</span></span>

<span data-ttu-id="6830b-189">LibMan を使用してインストールされたライブラリ ファイルを削除するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="6830b-190">ライブラリ ファイルのアンインストール</span><span class="sxs-lookup"><span data-stu-id="6830b-190">Uninstall library files</span></span>

<span data-ttu-id="6830b-191">`libman uninstall` コマンドは、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="6830b-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="6830b-192">指定したライブラリに関連付けられているすべてのファイルを *libman.json* のインストール先から削除します。</span><span class="sxs-lookup"><span data-stu-id="6830b-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="6830b-193">関連付けられたライブラリ構成を *libman.json* から削除します。</span><span class="sxs-lookup"><span data-stu-id="6830b-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="6830b-194">次の場合、エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="6830b-194">An error occurs when:</span></span>

* <span data-ttu-id="6830b-195">プロジェクト ルートに *libman.json* ファイルが存在しません。</span><span class="sxs-lookup"><span data-stu-id="6830b-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="6830b-196">指定されたライブラリが存在しません。</span><span class="sxs-lookup"><span data-stu-id="6830b-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="6830b-197">同じ名前のライブラリが複数インストールされている場合は、1 つを選択するように求められます。</span><span class="sxs-lookup"><span data-stu-id="6830b-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6830b-198">構文</span><span class="sxs-lookup"><span data-stu-id="6830b-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6830b-199">引数</span><span class="sxs-lookup"><span data-stu-id="6830b-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6830b-200">アンインストールするライブラリの名前。</span><span class="sxs-lookup"><span data-stu-id="6830b-200">The name of the library to uninstall.</span></span> <span data-ttu-id="6830b-201">この名前には、バージョン番号の表記 (`@1.2.0` など) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="6830b-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="6830b-202">オプション</span><span class="sxs-lookup"><span data-stu-id="6830b-202">Options</span></span>

<span data-ttu-id="6830b-203">`libman uninstall` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6830b-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6830b-204">使用例</span><span class="sxs-lookup"><span data-stu-id="6830b-204">Examples</span></span>

<span data-ttu-id="6830b-205">次の *libman.json* ファイルについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="6830b-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="6830b-206">jQuery をアンインストールするには、次のいずれかのコマンドを正常に実行します。</span><span class="sxs-lookup"><span data-stu-id="6830b-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="6830b-207">`filesystem` プロバイダーを使用してインストールされた Lodash ファイルをアンインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="6830b-208">ライブラリ バージョンの更新</span><span class="sxs-lookup"><span data-stu-id="6830b-208">Update library version</span></span>

<span data-ttu-id="6830b-209">`libman update` コマンドは、LibMan を使用してインストールされたライブラリを指定されたバージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="6830b-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="6830b-210">次の場合、エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="6830b-210">An error occurs when:</span></span>

* <span data-ttu-id="6830b-211">プロジェクト ルートに *libman.json* ファイルが存在しません。</span><span class="sxs-lookup"><span data-stu-id="6830b-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="6830b-212">指定されたライブラリが存在しません。</span><span class="sxs-lookup"><span data-stu-id="6830b-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="6830b-213">同じ名前のライブラリが複数インストールされている場合は、1 つを選択するように求められます。</span><span class="sxs-lookup"><span data-stu-id="6830b-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6830b-214">構文</span><span class="sxs-lookup"><span data-stu-id="6830b-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6830b-215">引数</span><span class="sxs-lookup"><span data-stu-id="6830b-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6830b-216">更新するライブラリの名前。</span><span class="sxs-lookup"><span data-stu-id="6830b-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="6830b-217">オプション</span><span class="sxs-lookup"><span data-stu-id="6830b-217">Options</span></span>

<span data-ttu-id="6830b-218">`libman update` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6830b-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="6830b-219">ライブラリの最新のプレリリース バージョンを取得します。</span><span class="sxs-lookup"><span data-stu-id="6830b-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="6830b-220">特定のバージョンのライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="6830b-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6830b-221">使用例</span><span class="sxs-lookup"><span data-stu-id="6830b-221">Examples</span></span>

* <span data-ttu-id="6830b-222">jQuery を最新バージョンに更新するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="6830b-223">jQuery をバージョン 3.3.1 に更新するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="6830b-224">jQuery を最新のプレリリース バージョンに更新するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="6830b-225">ライブラリ キャッシュの管理</span><span class="sxs-lookup"><span data-stu-id="6830b-225">Manage library cache</span></span>

<span data-ttu-id="6830b-226">`libman cache` コマンドは、LibMan ライブラリ キャッシュを管理します。</span><span class="sxs-lookup"><span data-stu-id="6830b-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="6830b-227">`filesystem` プロバイダーはライブラリ キャッシュを使用しません。</span><span class="sxs-lookup"><span data-stu-id="6830b-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6830b-228">構文</span><span class="sxs-lookup"><span data-stu-id="6830b-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6830b-229">引数</span><span class="sxs-lookup"><span data-stu-id="6830b-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="6830b-230">`clean` コマンドでのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-230">Only used with the `clean` command.</span></span> <span data-ttu-id="6830b-231">消去するプロバイダー キャッシュを指定します。</span><span class="sxs-lookup"><span data-stu-id="6830b-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="6830b-232">有効な値を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6830b-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="6830b-233">オプション</span><span class="sxs-lookup"><span data-stu-id="6830b-233">Options</span></span>

<span data-ttu-id="6830b-234">`libman cache` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6830b-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="6830b-235">キャッシュされているファイルの名前を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="6830b-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="6830b-236">キャッシュされているライブラリの名前を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="6830b-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6830b-237">使用例</span><span class="sxs-lookup"><span data-stu-id="6830b-237">Examples</span></span>

* <span data-ttu-id="6830b-238">キャッシュされたライブラリの名前をプロバイダーごとに表示するには、次のコマンドのいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="6830b-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="6830b-239">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-239">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* <span data-ttu-id="6830b-240">キャッシュされたライブラリ ファイルの名前をプロバイダーごとに表示するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="6830b-241">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6830b-241">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  <span data-ttu-id="6830b-242">前の出力から、jQuery バージョン 3.2.1 と 3.3.1 が CDNJS プロバイダーでキャッシュされていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="6830b-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="6830b-243">CDNJS プロバイダーのライブラリ キャッシュを空にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="6830b-244">CDNJS プロバイダーのキャッシュが空になると、`libman cache list` コマンドは次のように表示します。</span><span class="sxs-lookup"><span data-stu-id="6830b-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* <span data-ttu-id="6830b-245">サポートされているすべてのプロバイダーのキャッシュを空にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6830b-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="6830b-246">すべてのプロバイダーのキャッシュが空になると、`libman cache list` コマンドは次のように表示します。</span><span class="sxs-lookup"><span data-stu-id="6830b-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="6830b-247">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6830b-247">Additional resources</span></span>

* [<span data-ttu-id="6830b-248">グローバル ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="6830b-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="6830b-249">LibMan の GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="6830b-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
