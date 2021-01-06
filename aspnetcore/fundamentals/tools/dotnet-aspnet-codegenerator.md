---
title: dotnet aspnet-codegenerator コマンド
author: rick-anderson
description: dotnet aspnet-codegenerator コマンドでは、ASP.NET Core プロジェクトがスキャフォールディングされます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "94920704"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="4649c-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="4649c-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="4649c-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4649c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4649c-105">`dotnet aspnet-codegenerator` - ASP.NET Core のスキャフォールディング エンジンを実行します。</span><span class="sxs-lookup"><span data-stu-id="4649c-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="4649c-106">`dotnet aspnet-codegenerator` が必要になるのは、コマンド ラインからスキャフォールディングする場合のみです。Visual Studio でスキャフォールディングする場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4649c-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="4649c-107">aspnet-codegenerator のインストールと更新</span><span class="sxs-lookup"><span data-stu-id="4649c-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="4649c-108">[.NET SDK](https://dotnet.microsoft.com/download) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4649c-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="4649c-109">`dotnet-aspnet-codegenerator` は[グローバル ツール](/dotnet/core/tools/global-tools)であり、インストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4649c-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="4649c-110">次のコマンドを使うと、`dotnet-aspnet-codegenerator` ツールの最新の安定バージョンをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="4649c-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="4649c-111">次のコマンドを使うと、インストール済みの .NET Core SDK から利用できる更新の安定バージョンに `dotnet-aspnet-codegenerator` を更新できます。</span><span class="sxs-lookup"><span data-stu-id="4649c-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="4649c-112">aspnet-codegenerator のアンインストール</span><span class="sxs-lookup"><span data-stu-id="4649c-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="4649c-113">問題を解決するには、`aspnet-codegenerator` のアンインストールが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="4649c-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="4649c-114">たとえば、`aspnet-codegenerator` のプレビュー バージョンをインストールした場合は、リリース バージョンをインストールする前にアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="4649c-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="4649c-115">次のコマンドを使用すると、`dotnet-aspnet-codegenerator` ツールをアンインストールし、最新の安定バージョンをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="4649c-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="4649c-116">構文</span><span class="sxs-lookup"><span data-stu-id="4649c-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="4649c-117">説明</span><span class="sxs-lookup"><span data-stu-id="4649c-117">Description</span></span>

<span data-ttu-id="4649c-118">`dotnet aspnet-codegenerator` グローバル コマンドを使うと、ASP.NET Core のコード ジェネレーターとスキャフォールディング エンジンが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4649c-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="4649c-119">引数</span><span class="sxs-lookup"><span data-stu-id="4649c-119">Arguments</span></span>

`generator`

<span data-ttu-id="4649c-120">実行するコード ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="4649c-120">The code generator to run.</span></span> <span data-ttu-id="4649c-121">次のジェネレーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="4649c-121">The following generators are available:</span></span>

| <span data-ttu-id="4649c-122">Generator</span><span class="sxs-lookup"><span data-stu-id="4649c-122">Generator</span></span>  | <span data-ttu-id="4649c-123">操作</span><span class="sxs-lookup"><span data-stu-id="4649c-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="4649c-124">area</span><span class="sxs-lookup"><span data-stu-id="4649c-124">area</span></span>       | [<span data-ttu-id="4649c-125">区分のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="4649c-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="4649c-126">コントローラー</span><span class="sxs-lookup"><span data-stu-id="4649c-126">controller</span></span> | [<span data-ttu-id="4649c-127">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="4649c-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="4649c-128">ID</span><span class="sxs-lookup"><span data-stu-id="4649c-128">identity</span></span>   | [<span data-ttu-id="4649c-129">Identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="4649c-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="4649c-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="4649c-130">razorpage</span></span>  | [<span data-ttu-id="4649c-131">Razor Pages のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="4649c-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="4649c-132">ビュー</span><span class="sxs-lookup"><span data-stu-id="4649c-132">view</span></span>       | [<span data-ttu-id="4649c-133">ビューのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="4649c-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="4649c-134">オプション</span><span class="sxs-lookup"><span data-stu-id="4649c-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="4649c-135">NuGet パッケージのディレクトリを指定します。</span><span class="sxs-lookup"><span data-stu-id="4649c-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="4649c-136">ビルド構成を定義します。</span><span class="sxs-lookup"><span data-stu-id="4649c-136">Defines the build configuration.</span></span> <span data-ttu-id="4649c-137">既定値は `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="4649c-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="4649c-138">使用するターゲット [フレームワーク](/dotnet/standard/frameworks)です。</span><span class="sxs-lookup"><span data-stu-id="4649c-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="4649c-139">たとえば、`net46` のようにします。</span><span class="sxs-lookup"><span data-stu-id="4649c-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="4649c-140">ビルドのベース パスです。</span><span class="sxs-lookup"><span data-stu-id="4649c-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="4649c-141">コマンドの短いヘルプを印刷します。</span><span class="sxs-lookup"><span data-stu-id="4649c-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="4649c-142">実行前にプロジェクトをビルドしません。</span><span class="sxs-lookup"><span data-stu-id="4649c-142">Doesn't build the project before running.</span></span> <span data-ttu-id="4649c-143">また、`--no-restore` フラグが暗黙的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4649c-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="4649c-144">実行するプロジェクト ファイルのパスを指定します (フォルダー名または完全なパス)。</span><span class="sxs-lookup"><span data-stu-id="4649c-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="4649c-145">指定しない場合は、既定で現在のディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="4649c-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="4649c-146">ジェネレーターのオプション</span><span class="sxs-lookup"><span data-stu-id="4649c-146">Generator options</span></span>

<span data-ttu-id="4649c-147">以下のセクションでは、次のサポートされているジェネレーターで使用できるオプションについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="4649c-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="4649c-148">区分</span><span class="sxs-lookup"><span data-stu-id="4649c-148">Area</span></span>
* <span data-ttu-id="4649c-149">コントローラー</span><span class="sxs-lookup"><span data-stu-id="4649c-149">Controller</span></span>
* Identity  
* <span data-ttu-id="4649c-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="4649c-150">Razorpage</span></span>
* <span data-ttu-id="4649c-151">View</span><span class="sxs-lookup"><span data-stu-id="4649c-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="4649c-152">Area のオプション</span><span class="sxs-lookup"><span data-stu-id="4649c-152">Area options</span></span>

<span data-ttu-id="4649c-153">このツールは、コントローラーとビューを含む ASP.NET Core Web プロジェクト用のものです。</span><span class="sxs-lookup"><span data-stu-id="4649c-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="4649c-154">Razor Pages アプリ用ではありません。</span><span class="sxs-lookup"><span data-stu-id="4649c-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="4649c-155">使用方法: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="4649c-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="4649c-156">上のコマンドを実行すると、次のフォルダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="4649c-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="4649c-157">*領域*</span><span class="sxs-lookup"><span data-stu-id="4649c-157">*Areas*</span></span>
  * <span data-ttu-id="4649c-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="4649c-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="4649c-159">*コントローラー*</span><span class="sxs-lookup"><span data-stu-id="4649c-159">*Controllers*</span></span>
    * <span data-ttu-id="4649c-160">*データ*</span><span class="sxs-lookup"><span data-stu-id="4649c-160">*Data*</span></span>
    * <span data-ttu-id="4649c-161">*Models*</span><span class="sxs-lookup"><span data-stu-id="4649c-161">*Models*</span></span>
    * <span data-ttu-id="4649c-162">*ビュー*</span><span class="sxs-lookup"><span data-stu-id="4649c-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="4649c-163">Controller のオプション</span><span class="sxs-lookup"><span data-stu-id="4649c-163">Controller options</span></span>

<span data-ttu-id="4649c-164">`aspnet-codegenerator` `controller` および `razorpage` 用のオプションの一覧を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="4649c-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="4649c-165">`aspnet-codegenerator controller` に固有のオプションの一覧を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="4649c-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="4649c-166">オプション</span><span class="sxs-lookup"><span data-stu-id="4649c-166">Option</span></span>                         | <span data-ttu-id="4649c-167">説明</span><span class="sxs-lookup"><span data-stu-id="4649c-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="4649c-168">--controllerName または -name</span><span class="sxs-lookup"><span data-stu-id="4649c-168">--controllerName or -name</span></span>      | <span data-ttu-id="4649c-169">コントローラーの名前です。</span><span class="sxs-lookup"><span data-stu-id="4649c-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="4649c-170">--useAsyncActions または -async</span><span class="sxs-lookup"><span data-stu-id="4649c-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="4649c-171">非同期コントローラー アクションを生成します。</span><span class="sxs-lookup"><span data-stu-id="4649c-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="4649c-172">--noViews または -nv</span><span class="sxs-lookup"><span data-stu-id="4649c-172">--noViews or -nv</span></span>               | <span data-ttu-id="4649c-173">ビューを生成 **しません**。</span><span class="sxs-lookup"><span data-stu-id="4649c-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="4649c-174">--restWithNoViews または -api</span><span class="sxs-lookup"><span data-stu-id="4649c-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="4649c-175">REST スタイルの API でコントローラーを生成します。</span><span class="sxs-lookup"><span data-stu-id="4649c-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="4649c-176">`noViews` を前提とし、ビュー関連のオプションはすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="4649c-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="4649c-177">--readWriteActions または -actions</span><span class="sxs-lookup"><span data-stu-id="4649c-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="4649c-178">モデルなしで読み取り/書き込みアクションを備えたコントローラーを生成します。</span><span class="sxs-lookup"><span data-stu-id="4649c-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="4649c-179">`aspnet-codegenerator controller` コマンドに関するヘルプを取得するには、`-h` スイッチを使います。</span><span class="sxs-lookup"><span data-stu-id="4649c-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="4649c-180">`dotnet aspnet-codegenerator controller` の例については、[映画モデルのスキャフォールディング](xref:tutorials/first-mvc-app/adding-model)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4649c-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="4649c-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="4649c-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="4649c-182">新しいページの名前と使用するテンプレートを指定すれば、Razor Pages を個別にスキャフォールディングできます。</span><span class="sxs-lookup"><span data-stu-id="4649c-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="4649c-183">サポートされているテンプレートは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4649c-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="4649c-184">たとえば、次のコマンドでは、編集テンプレートを使って *MyEdit.cshtml* と *MyEdit.cshtml.cs* が生成されます。</span><span class="sxs-lookup"><span data-stu-id="4649c-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="4649c-185">通常、テンプレートと生成されたファイル名は指定されず、次のテンプレートが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4649c-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="4649c-186">`aspnet-codegenerator` `razorpage` および `controller` 用のオプションの一覧を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="4649c-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="4649c-187">`aspnet-codegenerator razorpage` に固有のオプションの一覧を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="4649c-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="4649c-188">オプション</span><span class="sxs-lookup"><span data-stu-id="4649c-188">Option</span></span>                        | <span data-ttu-id="4649c-189">説明</span><span class="sxs-lookup"><span data-stu-id="4649c-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="4649c-190">--namespaceName または -namespace</span><span class="sxs-lookup"><span data-stu-id="4649c-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="4649c-191">生成された PageModel に対して使用する名前空間の名前です</span><span class="sxs-lookup"><span data-stu-id="4649c-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="4649c-192">--partialView または -partial</span><span class="sxs-lookup"><span data-stu-id="4649c-192">--partialView or -partial</span></span>     | <span data-ttu-id="4649c-193">部分ビューを生成します。</span><span class="sxs-lookup"><span data-stu-id="4649c-193">Generate a partial view.</span></span> <span data-ttu-id="4649c-194">これを指定した場合、レイアウトのオプション -l と -udl は無視されます。</span><span class="sxs-lookup"><span data-stu-id="4649c-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="4649c-195">--noPageModel または -npm</span><span class="sxs-lookup"><span data-stu-id="4649c-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="4649c-196">空のテンプレートの PageModel クラスを生成しない動作に切り替えます</span><span class="sxs-lookup"><span data-stu-id="4649c-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="4649c-197">`aspnet-codegenerator razorpage` コマンドに関するヘルプを取得するには、`-h` スイッチを使います。</span><span class="sxs-lookup"><span data-stu-id="4649c-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="4649c-198">`dotnet aspnet-codegenerator razorpage` の例については、[映画モデルのスキャフォールディング](xref:tutorials/razor-pages/model)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4649c-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="4649c-199">[Identity のスキャフォールディング](xref:security/authentication/scaffold-identity)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4649c-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
