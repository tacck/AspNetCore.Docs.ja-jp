---
title: ファイル ウォッチャーを使用した ASP.NET Core アプリの開発
author: rick-anderson
description: このチュートリアルでは、.NET Core CLI のファイル ウォッチャー (dotnet watch) ツールをインストールし、ASP.NET Core アプリで使用する方法について説明します。
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 27420fe00ba6375e15b67fb359be06df055eff1f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060041"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="ebfab-103">ファイル ウォッチャーを使用した ASP.NET Core アプリの開発</span><span class="sxs-lookup"><span data-stu-id="ebfab-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="ebfab-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="ebfab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="ebfab-105">`dotnet watch` は、ソース ファイルの変更時に [.NET Core CLI](/dotnet/core/tools) コマンドを実行するツールです。</span><span class="sxs-lookup"><span data-stu-id="ebfab-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="ebfab-106">たとえば、あるファイルを変更すると、コンパイル、テストの実行、展開が開始されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="ebfab-107">このチュートリアルでは、エンドポイントが 2 つの既存の Web API を利用します。合計を返すエンドポイントと積を返すエンドポイントです。</span><span class="sxs-lookup"><span data-stu-id="ebfab-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="ebfab-108">積のメソッドにはバグがあり、このチュートリアルで修正します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="ebfab-109">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)をダウンロードしてください。</span><span class="sxs-lookup"><span data-stu-id="ebfab-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="ebfab-110">これには次の 2 つのプロジェクトが含まれています。*WebApp* (ASP.NET Core Web API) および *WebAppTests* (Web API の単体テスト)。</span><span class="sxs-lookup"><span data-stu-id="ebfab-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="ebfab-111">コマンド シェルで、*WebApp* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="ebfab-112">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="ebfab-113">`dotnet run --project <PROJECT>` を使用して、実行するプロジェクトを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="ebfab-114">たとえば、サンプル アプリのルートから `dotnet run --project WebApp` を実行すると、*WebApp* プロジェクトも実行されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="ebfab-115">コンソール出力に、次のようなメッセージが表示されます。アプリが実行中であり、要求を待っていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="ebfab-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="ebfab-116">Web ブラウザーで、`http://localhost:<port number>/api/math/sum?a=4&b=5` に移動します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="ebfab-117">結果として `9` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-117">You should see the result of `9`.</span></span>

<span data-ttu-id="ebfab-118">製品 API に移動します (`http://localhost:<port number>/api/math/product?a=4&b=5`)。</span><span class="sxs-lookup"><span data-stu-id="ebfab-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="ebfab-119">予想していた `20` ではなく、`9` が返されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="ebfab-120">この問題は、チュートリアルで後ほど修正します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="ebfab-121">`dotnet watch` をプロジェクトに追加する</span><span class="sxs-lookup"><span data-stu-id="ebfab-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="ebfab-122">`dotnet watch` ファイル ウォッチャー ツールは、.NET Core SDK のバージョン 2.1.300 に付属しています。</span><span class="sxs-lookup"><span data-stu-id="ebfab-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="ebfab-123">これより前のバージョンの .NET Core SDK を使用する場合は、次の手順が必要です。</span><span class="sxs-lookup"><span data-stu-id="ebfab-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="ebfab-124">`Microsoft.DotNet.Watcher.Tools` パッケージ参照を *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="ebfab-125">次のコマンドを実行して `Microsoft.DotNet.Watcher.Tools` パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="ebfab-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="ebfab-126">`dotnet watch` を使用した .NET Core CLI コマンドの実行</span><span class="sxs-lookup"><span data-stu-id="ebfab-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="ebfab-127">[.NET Core CLI コマンド](/dotnet/core/tools#cli-commands) はいずれも、`dotnet watch` との組み合わせで実行することができます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="ebfab-128">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-128">For example:</span></span>

| <span data-ttu-id="ebfab-129">コマンド</span><span class="sxs-lookup"><span data-stu-id="ebfab-129">Command</span></span> | <span data-ttu-id="ebfab-130">コマンドと watch</span><span class="sxs-lookup"><span data-stu-id="ebfab-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="ebfab-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="ebfab-131">dotnet run</span></span> | <span data-ttu-id="ebfab-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="ebfab-132">dotnet watch run</span></span> |
| <span data-ttu-id="ebfab-133">dotnet run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="ebfab-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="ebfab-134">dotnet watch run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="ebfab-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="ebfab-135">dotnet run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="ebfab-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="ebfab-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="ebfab-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="ebfab-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="ebfab-137">dotnet test</span></span> | <span data-ttu-id="ebfab-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="ebfab-138">dotnet watch test</span></span> |

<span data-ttu-id="ebfab-139">*WebApp* フォルダーの `dotnet watch run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="ebfab-140">コンソール出力に、`watch` が起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="ebfab-141">Web アプリで `dotnet watch run` を実行すると、準備完了後にブラウザーが起動して、そのアプリの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="ebfab-142">`dotnet watch` は、これを実行するために、アプリのコンソール出力を読み取り、<xref:Microsoft.AspNetCore.WebHost> によって表示される準備完了メッセージを待機します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="ebfab-143">`dotnet watch` によってウォッチ対象のファイルに対する変更が検出されると、ブラウザーが最新の情報に更新されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="ebfab-144">これを行うために、アプリによって作成された HTML 応答を変更するミドルウェアが、watch コマンドによってアプリに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="ebfab-145">このミドルウェアでは、`dotnet watch` がブラウザーに更新を指示する JavaScript スクリプト ブロックがページに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="ebfab-146">現在、ウォッチ対象のすべてのファイルに対する変更 ( *.html* ファイル、 *.css* ファイルなどの静的コンテンツ) によって、アプリが再ビルドされます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="ebfab-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="ebfab-147">`dotnet watch`:</span></span>

* <span data-ttu-id="ebfab-148">既定では、ビルドに影響するファイルのみをウォッチします。</span><span class="sxs-lookup"><span data-stu-id="ebfab-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="ebfab-149">追加でウォッチ対象となるファイル (構成により) でも、ビルドが行われます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="ebfab-150">構成の詳細については、このドキュメントの「[dotnet-watch の構成](#dotnet-watch-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ebfab-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="ebfab-151">`dotnet watch --project <PROJECT>` を使用して、ウォッチするプロジェクトを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="ebfab-152">たとえば、サンプル アプリのルートから `dotnet watch --project WebApp run` を実行すると、*WebApp* プロジェクトも実行されてウォッチされます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="ebfab-153">`dotnet watch` で変更を行う</span><span class="sxs-lookup"><span data-stu-id="ebfab-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="ebfab-154">`dotnet watch` が実行されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="ebfab-155">*MathController.cs* の `Product` メソッドのバグを修正して、合計ではなく積を返すようにします。</span><span class="sxs-lookup"><span data-stu-id="ebfab-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="ebfab-156">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-156">Save the file.</span></span> <span data-ttu-id="ebfab-157">コンソール出力により、`dotnet watch` がファイル変更を検出し、アプリを再起動したことが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="ebfab-158">`http://localhost:<port number>/api/math/product?a=4&b=5` が正しい結果を返すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="ebfab-159">`dotnet watch` を使用してテストを実行する</span><span class="sxs-lookup"><span data-stu-id="ebfab-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="ebfab-160">*MathController.cs* の `Product` メソッドを元に戻して合計を返すようにします。</span><span class="sxs-lookup"><span data-stu-id="ebfab-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="ebfab-161">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-161">Save the file.</span></span>
1. <span data-ttu-id="ebfab-162">コマンド シェルで、*WebAppTests* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="ebfab-163">[dotnet restore](/dotnet/core/tools/dotnet-restore) を実行します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="ebfab-164">`dotnet watch test` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="ebfab-165">テストに失敗し、ウォッチャーがファイル変更を待っていることが出力に示されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="ebfab-166">積を返すように `Product` メソッドのコードを修正します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="ebfab-167">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-167">Save the file.</span></span>

<span data-ttu-id="ebfab-168">`dotnet watch` はファイル変更を検出し、テストを再実行します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="ebfab-169">コンソール出力にテストの合格が示されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="ebfab-170">監視するファイル リストのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="ebfab-170">Customize files list to watch</span></span>

<span data-ttu-id="ebfab-171">既定では、`dotnet-watch` は次の glob パターンに一致するすべてのファイルを追跡します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="ebfab-172">ウォッチ リストに他の項目を追加するには、 *.csproj* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="ebfab-173">項目は個別に指定することも、glob パターンを使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="ebfab-174">ウォッチするファイルのオプトアウト</span><span class="sxs-lookup"><span data-stu-id="ebfab-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="ebfab-175">既定の設定を無視するように `dotnet-watch` を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="ebfab-176">特定のファイルを無視するには、 *.csproj* ファイルで項目の定義に `Watch="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="ebfab-177">カスタム ウォッチ プロジェクト</span><span class="sxs-lookup"><span data-stu-id="ebfab-177">Custom watch projects</span></span>

<span data-ttu-id="ebfab-178">`dotnet-watch` は C# プロジェクトだけに限定されていません。</span><span class="sxs-lookup"><span data-stu-id="ebfab-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="ebfab-179">カスタム ウォッチ プロジェクトは、さまざまなシナリオを処理するために作成できます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="ebfab-180">次のプロジェクト レイアウトを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ebfab-180">Consider the following project layout:</span></span>

* <span data-ttu-id="ebfab-181">**test/**</span><span class="sxs-lookup"><span data-stu-id="ebfab-181">**test/**</span></span>
  * <span data-ttu-id="ebfab-182">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="ebfab-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="ebfab-183">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="ebfab-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="ebfab-184">両方のプロジェクトを監視するのが目的である場合、両方のプロジェクトを監視するように構成されたカスタム プロジェクト ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="ebfab-185">両方のプロジェクトでファイルの監視を開始するには、*test* フォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="ebfab-186">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="ebfab-187">VSTest は、いずれかのテスト プロジェクトでファイルが変更されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="ebfab-188">dotnet-watch の構成</span><span class="sxs-lookup"><span data-stu-id="ebfab-188">dotnet-watch configuration</span></span>

<span data-ttu-id="ebfab-189">一部の構成オプションは、環境変数を通じて `dotnet watch` に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="ebfab-190">使用できる変数は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ebfab-190">The available variables are:</span></span>

| <span data-ttu-id="ebfab-191">設定</span><span class="sxs-lookup"><span data-stu-id="ebfab-191">Setting</span></span>  | <span data-ttu-id="ebfab-192">説明</span><span class="sxs-lookup"><span data-stu-id="ebfab-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="ebfab-193">"1" または "true" に設定した場合、`dotnet watch` によって、CoreFx の `FileSystemWatcher` ではなく、ポーリング ファイル ウォッチャーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="ebfab-194">ネットワーク共有または Docker でマウントされたボリューム上のファイルをウォッチするときに使用します。</span><span class="sxs-lookup"><span data-stu-id="ebfab-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="ebfab-195">既定では、復元の実行や、ファイルが変更されるたびにウォッチ対象のファイルのセットの再評価が行われるなどの特定の操作が実行されないようにすることで、`dotnet watch` によってビルドが最適化されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="ebfab-196">"1" または "true" に設定した場合、これらの最適化は無効になります。</span><span class="sxs-lookup"><span data-stu-id="ebfab-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="ebfab-197">`dotnet watch run` は、*launchSettings.json* で構成された `launchBrowser` を使用して、Web アプリ用のブラウザーの起動を試みます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json*.</span></span> <span data-ttu-id="ebfab-198">"1" または "true" に設定した場合、この動作は抑制されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="ebfab-199">`dotnet watch run` は、ファイルの変更を検出すると、ブラウザーを最新の情報に更新することを試みます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="ebfab-200">"1" または "true" に設定した場合、この動作は抑制されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="ebfab-201">`DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` が設定されている場合も、この動作は抑制されます。</span><span class="sxs-lookup"><span data-stu-id="ebfab-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="ebfab-202">GitHub での `dotnet-watch`</span><span class="sxs-lookup"><span data-stu-id="ebfab-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="ebfab-203">`dotnet-watch` は GitHub の [aspnet/AspNetCore リポジトリ](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="ebfab-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
