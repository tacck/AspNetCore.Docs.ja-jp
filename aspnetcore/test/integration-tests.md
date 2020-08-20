---
title: ASP.NET Core での統合テスト
author: rick-anderson
description: 統合テストによってデータベース、ファイル システム、ネットワークなどのインフラストラクチャ レベルで、アプリのコンポーネントがどのように正しく機能するようになるかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: test/integration-tests
ms.openlocfilehash: b06c06fb5e525a0bdc3df1de50236fa8f76daca9
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635113"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="62024-103">ASP.NET Core での統合テスト</span><span class="sxs-lookup"><span data-stu-id="62024-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="62024-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Steve Smith](https://ardalis.com/)、[Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="62024-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="62024-105">統合テストでは、データベース、ファイル システム、ネットワークなど、アプリのサポート インフラストラクチャを含むレベルで、アプリのコンポーネントが正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="62024-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="62024-106">ASP.NET Core では、単体テスト フレームワークとテスト Web ホストおよびメモリ内テスト サーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="62024-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="62024-107">このトピックを読むには、単体テストの基本的な知識があることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="62024-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="62024-108">テストの概念を理解していない場合は、「[.NET Core の単体テストと .NET Standard」](/dotnet/core/testing/) のトピックとリンクされたコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="62024-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="62024-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="62024-110">このサンプル アプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="62024-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="62024-111">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="62024-112">Razor Pages の概要</span><span class="sxs-lookup"><span data-stu-id="62024-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="62024-113">Razor Pages の使用を開始する</span><span class="sxs-lookup"><span data-stu-id="62024-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="62024-114">Razor Pages の単体テスト</span><span class="sxs-lookup"><span data-stu-id="62024-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="62024-115">SPA をテストする場合は、ブラウザーを自動化できる [Selenium](https://www.seleniumhq.org/) などのツールを推奨します。</span><span class="sxs-lookup"><span data-stu-id="62024-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="62024-116">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="62024-116">Introduction to integration tests</span></span>

<span data-ttu-id="62024-117">統合テストでは [単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="62024-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="62024-118">単体テストは、個々のクラス メソッドなど、分離されたソフトウェア コンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="62024-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="62024-119">統合テストでは、2 つ以上のアプリ コンポーネントが、連携して予想される結果を生成することを確認します。これは、要求を完全に処理するために必要なすべてのコンポーネントを含む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="62024-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="62024-120">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="62024-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="62024-121">データベース</span><span class="sxs-lookup"><span data-stu-id="62024-121">Database</span></span>
* <span data-ttu-id="62024-122">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="62024-122">File system</span></span>
* <span data-ttu-id="62024-123">ネットワーク アプライアンス</span><span class="sxs-lookup"><span data-stu-id="62024-123">Network appliances</span></span>
* <span data-ttu-id="62024-124">要求 - 応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="62024-124">Request-response pipeline</span></span>

<span data-ttu-id="62024-125">単体テストでは、インフラストラクチャ コンポーネントの代わりに、*フェイク*または*モック オブジェクト*と呼ばれる、作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="62024-126">単体テストと比較すると、統合テストは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="62024-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="62024-127">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="62024-128">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="62024-128">Require more code and data processing.</span></span>
* <span data-ttu-id="62024-129">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="62024-129">Take longer to run.</span></span>

<span data-ttu-id="62024-130">そのため、統合テストの使用は最も重要なインフラストラクチャ シナリオに限定します。</span><span class="sxs-lookup"><span data-stu-id="62024-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="62024-131">単体テストと統合テストのどちらを使用しても動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="62024-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="62024-132">統合テストは、データベースとファイル システムを使用するデータおよびファイル アクセスのすべての順列として記述してはいけません。</span><span class="sxs-lookup"><span data-stu-id="62024-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="62024-133">通常は、アプリ全体でデータベースやファイル システムを操作する場所がいくつあったとしても、的を絞った一連の読み取り、書き込み、更新、削除の統合テストを行うことで、データベースおよびファイル システム コンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="62024-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="62024-134">これらのコンポーネントと連携するメソッドのロジックのルーチン テストには、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="62024-135">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="62024-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="62024-136">統合テストの説明では、テスト対象のプロジェクトをよく*テスト対象システム*、または短縮して "SUT" と呼びます。</span><span class="sxs-lookup"><span data-stu-id="62024-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="62024-137">*このトピック全体で、テスト対象の ASP.NET Core アプリを指すために "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="62024-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="62024-138">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="62024-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="62024-139">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="62024-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="62024-140">テスト プロジェクトは、テストを格納して実行するために使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="62024-141">テスト プロジェクトは SUT への参照を含みます。</span><span class="sxs-lookup"><span data-stu-id="62024-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="62024-142">テスト プロジェクトは、SUT のテスト Web ホストを作成し、テスト サーバー クライアントを使用して SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="62024-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="62024-143">テスト ランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="62024-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="62024-144">統合テストでは、通常の *Arrange (配置)* 、*Act (実行)* 、および *Assert (確認)* のテスト ステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="62024-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="62024-145">SUT の Web ホストが構成されます。</span><span class="sxs-lookup"><span data-stu-id="62024-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="62024-146">アプリに要求を送信するためのテスト サーバー クライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="62024-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="62024-147">*Arrange (配置)* テスト ステップが実行されます。テスト アプリが要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="62024-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="62024-148">*Act (実行)* テスト ステップが実行されます。クライアントは要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="62024-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="62024-149">*Assert (確認)* テスト ステップが実行されます。*実際*の応答は、*予測される*応答に基づき、*成功*または*失敗*として検証されます。</span><span class="sxs-lookup"><span data-stu-id="62024-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="62024-150">このプロセスは、すべてのテストが実行されるまで続行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="62024-151">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="62024-151">The test results are reported.</span></span>

<span data-ttu-id="62024-152">通常、テスト Web ホストは、アプリの通常のテスト用の Web ホストとは異なる方法で構成されています。</span><span class="sxs-lookup"><span data-stu-id="62024-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="62024-153">たとえば、テスト用に別のデータベースまたは異なるアプリ設定を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="62024-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="62024-154">テスト Web ホストやメモリ内テスト サーバー ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャ コンポーネントは、[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="62024-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="62024-155">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="62024-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="62024-156">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="62024-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="62024-157">依存関係ファイル ( *.deps*) を SUT からテスト プロジェクトの *bin* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="62024-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="62024-158">テストを実行したときに、静的なファイルとページ/ビューが検出されるように、[コンテンツ ルート](xref:fundamentals/index#content-root)を SUT のプロジェクト ルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="62024-159">[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) クラスを提供し、`TestServer` を使用して SUT のブートストラップを効率化します。</span><span class="sxs-lookup"><span data-stu-id="62024-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="62024-160">[単体テストの](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)ドキュメントでは、テスト プロジェクトとテスト ランナーを設定する方法、テストを実行する方法の詳細な手順、テストおよびテスト クラスの命名方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="62024-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="62024-161">アプリのテスト プロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="62024-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="62024-162">これにより、インフラストラクチャ テスト コンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="62024-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="62024-163">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="62024-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="62024-164">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いがありません。</span><span class="sxs-lookup"><span data-stu-id="62024-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="62024-165">唯一の違いは、テストの命名方法です。</span><span class="sxs-lookup"><span data-stu-id="62024-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="62024-166">Razor Pages アプリでは、ページ エンドポイントのテストは通常、ページ モデル クラスにちなんだ名前が付けられます (たとえば、`IndexPageTests` では Index ページのコンポーネントの統合テストが行われます)。</span><span class="sxs-lookup"><span data-stu-id="62024-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="62024-167">MVC アプリでは、テストは通常、コントローラー クラス別に編成され、テストするコントローラーにちなんだ名前が付けられます (たとえば、`HomeControllerTests` は Home コントローラーのコンポーネントの統合テストを行います)。</span><span class="sxs-lookup"><span data-stu-id="62024-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="62024-168">テスト アプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="62024-168">Test app prerequisites</span></span>

<span data-ttu-id="62024-169">テスト プロジェクトは、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-169">The test project must:</span></span>

* <span data-ttu-id="62024-170">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージを参照しています。</span><span class="sxs-lookup"><span data-stu-id="62024-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="62024-171">プロジェクト ファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定しています。</span><span class="sxs-lookup"><span data-stu-id="62024-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="62024-172">これらの前提条件は、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で確認できます。</span><span class="sxs-lookup"><span data-stu-id="62024-172">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="62024-173">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="62024-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="62024-174">このサンプル アプリでは、[xUnit](https://xunit.github.io/) テスト フレームワークと [AngleSharp](https://anglesharp.github.io/) パーサー ライブラリを使用するので、サンプル アプリは以下も参照します。</span><span class="sxs-lookup"><span data-stu-id="62024-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="62024-175">xunit</span><span class="sxs-lookup"><span data-stu-id="62024-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="62024-176">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="62024-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="62024-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="62024-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="62024-178">テストでは Entity Framework Core も使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="62024-179">アプリは以下を参照します。</span><span class="sxs-lookup"><span data-stu-id="62024-179">The app references:</span></span>

* [<span data-ttu-id="62024-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="62024-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="62024-181">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="62024-181">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="62024-182">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="62024-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="62024-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="62024-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="62024-184">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="62024-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="62024-185">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="62024-185">SUT environment</span></span>

<span data-ttu-id="62024-186">SUT の [環境](xref:fundamentals/environments) が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="62024-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="62024-187">既定の WebApplicationFactory を使用した基本的なテスト</span><span class="sxs-lookup"><span data-stu-id="62024-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="62024-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) は、統合テスト用の [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="62024-189">`TEntryPoint` は SUT のエントリ ポイント クラスであり、通常は `Startup` クラスです。</span><span class="sxs-lookup"><span data-stu-id="62024-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="62024-190">テスト クラスは、クラスにテストが含まれていることを示すために*クラス フィクスチャ* インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内テストの共有オブジェクト インスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="62024-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="62024-191">次のテスト クラス `BasicTests` は、`WebApplicationFactory` を使用して SUT をブートストラップし、テスト メソッド `Get_EndpointsReturnSuccessAndCorrectContentType` に [HttpClient](/dotnet/api/system.net.http.httpclient) を提供します。</span><span class="sxs-lookup"><span data-stu-id="62024-191">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="62024-192">このメソッドは、複数のアプリ ページで応答状態コードが成功かどうか (200-299 の範囲の状態コード) と、`Content-Type` ヘッダーが `text/html; charset=utf-8` であるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="62024-192">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="62024-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) は `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトに従い、cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="62024-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="62024-194">既定では、[GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、要求間で必須でない cookie は保持されません。</span><span class="sxs-lookup"><span data-stu-id="62024-194">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="62024-195">TempData プロバイダーで使用されているような必須ではない cookie を保持するには、テストに必須であることをマークします。</span><span class="sxs-lookup"><span data-stu-id="62024-195">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="62024-196">cookie を必須としてマークする手順については、[必須 cookie](xref:security/gdpr#essential-cookies) に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62024-196">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="62024-197">WebApplicationFactory のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="62024-197">Customize WebApplicationFactory</span></span>

<span data-ttu-id="62024-198">Web ホストの構成は、`WebApplicationFactory` から継承して 1 つ以上のカスタム ファクトリを作成することで、テスト クラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="62024-198">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="62024-199">`WebApplicationFactory` から継承し、[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost) をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="62024-199">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="62024-200">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) では、[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) を使用してサービス コレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="62024-200">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="62024-201">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-201">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="62024-202">このメソッドについては、[統合テストのサンプル: テスト アプリの構成](#test-app-organization)に関するセクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62024-202">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="62024-203">SUT のデータベース コンテキストは、`Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="62024-203">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="62024-204">テスト アプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行された*後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-204">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="62024-205">ASP.NET Core 3.0 リリースの[汎用ホスト](xref:fundamentals/host/generic-host)により、実行順序に関する互換性のない変更が行われています。</span><span class="sxs-lookup"><span data-stu-id="62024-205">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="62024-206">アプリのデータベースとは異なるデータベースをテストに使用するには、`builder.ConfigureServices` でアプリのデータベース コンテキストを置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-206">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="62024-207">まだ [Web ホスト](xref:fundamentals/host/web-host)を使用している SUT の場合、テスト アプリの `builder.ConfigureServices` コールバックは、SUT の `Startup.ConfigureServices` コードの "*前に*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-207">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="62024-208">テスト アプリの `builder.ConfigureTestServices` コールバックは、"*後で*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-208">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="62024-209">サンプル アプリでは、データベース コンテキストのサービス記述子を検索し、記述子を使用してサービス登録を削除しています。</span><span class="sxs-lookup"><span data-stu-id="62024-209">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="62024-210">次に、ファクトリは、テストにメモリ内データベースを使用する新しい `ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="62024-210">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="62024-211">メモリ内データベースではないデータベースに接続するには、`UseInMemoryDatabase` 呼び出しを変更して、コンテキストを別のデータベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="62024-211">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="62024-212">SQL Server テスト データベースを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="62024-212">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="62024-213">プロジェクト ファイルで [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="62024-213">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="62024-214">データベースへの接続文字列を使用して `UseSqlServer` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="62024-214">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="62024-215">テスト クラスでカスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-215">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="62024-216">次の例では、`IndexPageTests` クラスでファクトリを使用しています。</span><span class="sxs-lookup"><span data-stu-id="62024-216">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="62024-217">サンプル アプリのクライアントは、`HttpClient` がリダイレクトに従わないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="62024-217">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="62024-218">[モック認証](#mock-authentication)のセクションで後述するように、これによってアプリの最初の応答結果を確認するテストが可能になります。</span><span class="sxs-lookup"><span data-stu-id="62024-218">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="62024-219">これらのテストでは、多くの場合、最初の応答は `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="62024-219">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="62024-220">一般的なテストでは、`HttpClient` およびヘルパー メソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="62024-220">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="62024-221">SUT に対する POST 要求は、アプリの[偽造防止データ保護システム](xref:security/data-protection/introduction)によって自動的に行われる偽造防止チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-221">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="62024-222">テストで POST 要求を実行するには、テスト アプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-222">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="62024-223">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="62024-223">Make a request for the page.</span></span>
1. <span data-ttu-id="62024-224">応答の偽造防止 cookie と要求検証トークンを解析します。</span><span class="sxs-lookup"><span data-stu-id="62024-224">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="62024-225">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="62024-225">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="62024-226">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパー メソッド (*Helpers/HtmlHelpers.cs*) は、次のメソッドで [AngleSharp](https://anglesharp.github.io/) パーサーを使用して偽造防止チェック処理を行います。</span><span class="sxs-lookup"><span data-stu-id="62024-226">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="62024-227">`GetDocumentAsync`:[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="62024-227">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="62024-228">`GetDocumentAsync` は、元の `HttpResponseMessage` に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-228">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="62024-229">詳しくは、[AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62024-229">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="62024-230">`HttpClient` の `SendAsync` 拡張メソッドは、[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) を作成し、[SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="62024-230">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="62024-231">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="62024-231">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="62024-232">フォームの送信ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="62024-232">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="62024-233">フォームの値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="62024-233">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="62024-234">送信ボタン (`IHtmlElement`) とフォームの値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="62024-234">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="62024-235">[AngleSharp](https://anglesharp.github.io/) は、このトピックとサンプル アプリのデモンストレーションのために使用するサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="62024-235">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="62024-236">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="62024-236">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="62024-237">[Html Agility Pack (HAP)](https://html-agility-pack.net/) などの他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="62024-237">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="62024-238">もう 1 つの方法として、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造防止 cookie を直接処理する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="62024-238">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="62024-239">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="62024-239">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="62024-240">テスト メソッド内で追加の構成が必要な場合は、[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) が構成によってさらにカスタマイズされた [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) を使用して新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="62024-240">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="62024-241">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テスト メソッドは、`WithWebHostBuilder` の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="62024-241">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="62024-242">このテストでは、SUT からフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="62024-242">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="62024-243">`IndexPageTests` クラス内の別のテストは、データベース内のすべてのレコードを削除する操作を実行します。この操作が `Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行される可能性があるため、このテスト メソッド内でデータベースの再シード処理を行い、確実に SUT が削除するレコードが存在するようにしています。</span><span class="sxs-lookup"><span data-stu-id="62024-243">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="62024-244">SUT に対する要求では、SUT の `messages` フォームの最初の [削除] ボタンの選択がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="62024-244">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="62024-245">クライアントのオプション</span><span class="sxs-lookup"><span data-stu-id="62024-245">Client options</span></span>

<span data-ttu-id="62024-246">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示します。</span><span class="sxs-lookup"><span data-stu-id="62024-246">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="62024-247">オプション</span><span class="sxs-lookup"><span data-stu-id="62024-247">Option</span></span> | <span data-ttu-id="62024-248">説明</span><span class="sxs-lookup"><span data-stu-id="62024-248">Description</span></span> | <span data-ttu-id="62024-249">Default</span><span class="sxs-lookup"><span data-stu-id="62024-249">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="62024-250">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="62024-250">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="62024-251">`HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-251">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="62024-252">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="62024-252">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="62024-253">`HttpClient` インスタンスのベース アドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-253">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| <span data-ttu-id="62024-254">[HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)</span><span class="sxs-lookup"><span data-stu-id="62024-254">[HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)</span></span> | <span data-ttu-id="62024-255">`HttpClient` インスタンスが cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-255">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="62024-256">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="62024-256">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="62024-257">`HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-257">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="62024-258">7</span><span class="sxs-lookup"><span data-stu-id="62024-258">7</span></span> |

<span data-ttu-id="62024-259">`WebApplicationFactoryClientOptions` クラスを作成し、それを [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="62024-259">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="62024-260">モック サービスの注入</span><span class="sxs-lookup"><span data-stu-id="62024-260">Inject mock services</span></span>

<span data-ttu-id="62024-261">ホスト ビルダーで [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) を呼び出すと、テストでサービスをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="62024-261">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="62024-262">**モック サービスを注入するには、SUT に `Startup` クラスが存在し、そこに `Startup.ConfigureServices` メソッドが存在している必要があります。**</span><span class="sxs-lookup"><span data-stu-id="62024-262">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="62024-263">サンプルの SUT には、引用符を返すスコープ サービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="62024-263">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="62024-264">インデックス ページが要求されると、インデックス ページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="62024-264">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="62024-265">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-265">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="62024-266">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-266">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="62024-267">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-267">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="62024-268">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-268">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="62024-269">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-269">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="62024-270">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="62024-270">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="62024-271">統合テストでサービスと引用符の注入をテストするため、テストは SUT にモック サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="62024-271">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="62024-272">モック サービスは、アプリの `QuoteService` をテスト アプリが提供する `TestQuoteService` と呼ばれるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="62024-272">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="62024-273">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-273">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="62024-274">`ConfigureTestServices` が呼び出され、スコープ サービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="62024-274">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="62024-275">`TestQuoteService` によって指定された引用符テキストがテストの実行中に生成されたマークアップに反映されるため、アサーションは成功します。</span><span class="sxs-lookup"><span data-stu-id="62024-275">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="62024-276">モック認証</span><span class="sxs-lookup"><span data-stu-id="62024-276">Mock authentication</span></span>

<span data-ttu-id="62024-277">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="62024-277">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="62024-278">認証されていないユーザーは、アプリのログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="62024-278">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="62024-279">認証されたユーザーには、コンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="62024-279">Returns content for an authenticated user.</span></span>

<span data-ttu-id="62024-280">SUT の `/SecurePage` ページは、[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 規約を使用してページに [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) を適用します。</span><span class="sxs-lookup"><span data-stu-id="62024-280">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="62024-281">詳細については、[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-281">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="62024-282">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) を `false` に設定することで、[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) がリダイレクトを許可しないように設定しています。</span><span class="sxs-lookup"><span data-stu-id="62024-282">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="62024-283">クライアントがリダイレクトに従うことを許可しないことで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="62024-283">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="62024-284">ログイン ページにリダイレクトした後の最終的な状態コード (これは [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode) になります) ではなく、予期される [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) の結果を SUT が返すステータス コードと突き合わせて確認できます。</span><span class="sxs-lookup"><span data-stu-id="62024-284">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="62024-285">最終的なログイン ページ応答 (ここでは、`Location` ヘッダーは存在しません) ではなく、応答ヘッダーの `Location` ヘッダー値が `http://localhost/Identity/Account/Login` で始まることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="62024-285">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="62024-286">テスト アプリでは、認証と承認の側面をテストするために [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="62024-286">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="62024-287">最小のシナリオでは、[AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="62024-287">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="62024-288">`TestAuthHandler` は、認証スキームが `Test` に設定されている場合 (この場合、`AddAuthentication` が `ConfigureTestServices` に登録されています) に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="62024-288">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`.</span></span> <span data-ttu-id="62024-289">アプリから要求されるスキームと `Test` スキームを一致させることが重要です。</span><span class="sxs-lookup"><span data-stu-id="62024-289">It's important for the `Test` scheme to match the scheme your app expects.</span></span> <span data-ttu-id="62024-290">そうでない場合は、認証が機能しません。</span><span class="sxs-lookup"><span data-stu-id="62024-290">Otherwise, authentication won't work.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="62024-291">`WebApplicationFactoryClientOptions` の詳細については、「[クライアントのオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-291">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="62024-292">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="62024-292">Set the environment</span></span>

<span data-ttu-id="62024-293">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="62024-293">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="62024-294">`IHostBuilder` を使用しているときに SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="62024-294">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="62024-295">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing` などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-295">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="62024-296">テスト アプリで `CreateHostBuilder` をオーバーライドして、`ASPNETCORE` で始まる環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="62024-296">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="62024-297">SUT が Web ホスト (`IWebHostBuilder`) を使用している場合は、`CreateWebHostBuilder` をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="62024-297">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="62024-298">テスト インフラストラクチャがアプリ コンテンツのルート パスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="62024-298">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="62024-299">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリの `System.Reflection.Assembly.FullName` と同じキーを持つ統合テストを含むアセンブリで [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) を検索することによって、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パスを推測します。</span><span class="sxs-lookup"><span data-stu-id="62024-299">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="62024-300">正しいキーを持つ属性が見つからない場合、`WebApplicationFactory` はフォールバックしてソリューション ファイル ( *.sln*) を検索し、`TEntryPoint` アセンブリ名をソリューション ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="62024-300">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="62024-301">アプリのルート ディレクトリ (コンテンツ ルート パス) は、ビューやコンテンツのファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="62024-301">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="62024-302">シャドウ コピーの無効化</span><span class="sxs-lookup"><span data-stu-id="62024-302">Disable shadow copying</span></span>

<span data-ttu-id="62024-303">シャドウ コピーを行うと、テストが出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-303">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="62024-304">テストが適切に機能するように、シャドウ コピーを無効化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-304">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="62024-305">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では、xUnit を使用して正しい構成設定の *xunit.runner.json* ファイルを含めることにより、xUnit のシャドウ コピーを無効化しています。</span><span class="sxs-lookup"><span data-stu-id="62024-305">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="62024-306">詳しくは、[JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62024-306">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="62024-307">次の内容を使用して、テスト プロジェクトのルートに *xunit.runner.json* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="62024-307">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="62024-308">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="62024-308">Disposal of objects</span></span>

<span data-ttu-id="62024-309">`IClassFixture` 実装のテストを実行した後、xUnit が [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) を破棄すると、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) と [HttpClient](/dotnet/api/system.net.http.httpclient) が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="62024-309">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="62024-310">開発者がインスタンス化したオブジェクトを破棄する必要がある場合は、`IClassFixture` の実装で破棄します。</span><span class="sxs-lookup"><span data-stu-id="62024-310">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="62024-311">詳細については、「[Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-311">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="62024-312">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="62024-312">Integration tests sample</span></span>

<span data-ttu-id="62024-313">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="62024-313">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="62024-314">アプリ</span><span class="sxs-lookup"><span data-stu-id="62024-314">App</span></span> | <span data-ttu-id="62024-315">プロジェクト ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="62024-315">Project directory</span></span> | <span data-ttu-id="62024-316">説明</span><span class="sxs-lookup"><span data-stu-id="62024-316">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="62024-317">メッセージ アプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="62024-317">Message app (the SUT)</span></span> | <span data-ttu-id="62024-318">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="62024-318">*src/RazorPagesProject*</span></span> | <span data-ttu-id="62024-319">ユーザーは、メッセージの追加、1 つ削除、すべて削除、および分析を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="62024-319">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="62024-320">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="62024-320">Test app</span></span> | <span data-ttu-id="62024-321">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="62024-321">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="62024-322">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="62024-322">Used to integration test the SUT.</span></span> |

<span data-ttu-id="62024-323">テストは、[Visual Studio](https://visualstudio.microsoft.com) などの IDE に組み込まれているテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="62024-323">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="62024-324">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用している場合は、コマンド プロンプトで *tests/RazorPagesProject.Tests* ディレクトリを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="62024-324">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="62024-325">メッセージ アプリ (SUT) の構成</span><span class="sxs-lookup"><span data-stu-id="62024-325">Message app (SUT) organization</span></span>

<span data-ttu-id="62024-326">SUT は、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="62024-326">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="62024-327">アプリのインデックス ページ (*Pages/Index.cshtml* と *Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="62024-327">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="62024-328">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="62024-328">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="62024-329">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="62024-329">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="62024-330">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="62024-330">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="62024-331">アプリのデータベース コンテキスト クラスである `AppDbContext` (*Data/AppDbContext.cs*) には、データアクセス層 (DAL) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="62024-331">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="62024-332">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="62024-332">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="62024-333">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="62024-333">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="62024-334">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="62024-334">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="62024-335">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-335">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="62024-336">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="62024-336">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="62024-337">アプリはリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="62024-337">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="62024-338">詳細については、[インフラストラクチャの永続化レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)および[コントローラー ロジックのテスト](/aspnet/core/mvc/controllers/testing)に関する記事を参照してください (このサンプルはリポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="62024-338">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="62024-339">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="62024-339">Test app organization</span></span>

<span data-ttu-id="62024-340">テスト アプリは、*tests/RazorPagesProject.Tests* ディレクトリにあるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="62024-340">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="62024-341">テスト アプリのディレクトリ</span><span class="sxs-lookup"><span data-stu-id="62024-341">Test app directory</span></span> | <span data-ttu-id="62024-342">説明</span><span class="sxs-lookup"><span data-stu-id="62024-342">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="62024-343">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="62024-343">*AuthTests*</span></span> | <span data-ttu-id="62024-344">次のテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="62024-344">Contains test methods for:</span></span><ul><li><span data-ttu-id="62024-345">認証されていないユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="62024-345">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="62024-346">モック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> で認証されたユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="62024-346">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="62024-347">GitHub ユーザー プロファイルを取得し、プロファイルのユーザー ログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="62024-347">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="62024-348">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="62024-348">*BasicTests*</span></span> | <span data-ttu-id="62024-349">ルーティングおよびコンテンツ タイプのテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="62024-349">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="62024-350">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="62024-350">*IntegrationTests*</span></span> | <span data-ttu-id="62024-351">カスタム `WebApplicationFactory` クラスを使用したインデックス ページの統合テストを含みます。</span><span class="sxs-lookup"><span data-stu-id="62024-351">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="62024-352">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="62024-352">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="62024-353">*Utilities.cs* には、データベースにテスト データをシードする `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="62024-353">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="62024-354">*HtmlHelpers.cs* には、テスト メソッドで使用する AngleSharp `IHtmlDocument` を返すメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="62024-354">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="62024-355">*HttpClientExtensions.cs* は、SUT に要求を送信する `SendAsync` のオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="62024-355">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="62024-356">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="62024-356">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="62024-357">統合テストは、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を含む [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-357">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="62024-358">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージはテスト ホストとテスト サーバーを構成するために使用されるため、テスト アプリのプロジェクト ファイルまたはテスト アプリの開発者構成で直接 `TestHost` および `TestServer` パッケージを参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="62024-358">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="62024-359">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="62024-359">**Seeding the database for testing**</span></span>

<span data-ttu-id="62024-360">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="62024-360">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="62024-361">たとえば、削除テストでは、データベース レコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも 1 つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="62024-361">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="62024-362">このサンプル アプリでは、*Utilities.cs* で 3 つのメッセージを使用してデータベースをシードします。このメッセージは、テストを実行する際に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="62024-362">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="62024-363">SUT のデータベース コンテキストは、`Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="62024-363">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="62024-364">テスト アプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行された*後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-364">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="62024-365">テストに異なるデータベースを使用するには、アプリのデータベース コンテキストを `builder.ConfigureServices`で置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-365">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="62024-366">詳細については、「[WebApplicationFactory のカスタマイズ](#customize-webapplicationfactory)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62024-366">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="62024-367">まだ [Web ホスト](xref:fundamentals/host/web-host)を使用している SUT の場合、テスト アプリの `builder.ConfigureServices` コールバックは、SUT の `Startup.ConfigureServices` コードの "*前に*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-367">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="62024-368">テスト アプリの `builder.ConfigureTestServices` コールバックは、"*後で*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-368">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="62024-369">統合テストでは、データベース、ファイル システム、ネットワークなど、アプリのサポート インフラストラクチャを含むレベルで、アプリのコンポーネントが正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="62024-369">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="62024-370">ASP.NET Core では、単体テスト フレームワークとテスト Web ホストおよびメモリ内テスト サーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="62024-370">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="62024-371">このトピックを読むには、単体テストの基本的な知識があることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="62024-371">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="62024-372">テストの概念を理解していない場合は、「[.NET Core の単体テストと .NET Standard」](/dotnet/core/testing/) のトピックとリンクされたコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-372">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="62024-373">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="62024-373">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="62024-374">このサンプル アプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="62024-374">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="62024-375">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-375">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="62024-376">Razor Pages の概要</span><span class="sxs-lookup"><span data-stu-id="62024-376">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="62024-377">Razor Pages の使用を開始する</span><span class="sxs-lookup"><span data-stu-id="62024-377">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="62024-378">Razor Pages の単体テスト</span><span class="sxs-lookup"><span data-stu-id="62024-378">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="62024-379">SPA をテストする場合は、ブラウザーを自動化できる [Selenium](https://www.seleniumhq.org/) などのツールを推奨します。</span><span class="sxs-lookup"><span data-stu-id="62024-379">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="62024-380">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="62024-380">Introduction to integration tests</span></span>

<span data-ttu-id="62024-381">統合テストでは [単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="62024-381">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="62024-382">単体テストは、個々のクラス メソッドなど、分離されたソフトウェア コンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="62024-382">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="62024-383">統合テストでは、2 つ以上のアプリ コンポーネントが、連携して予想される結果を生成することを確認します。これは、要求を完全に処理するために必要なすべてのコンポーネントを含む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="62024-383">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="62024-384">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="62024-384">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="62024-385">データベース</span><span class="sxs-lookup"><span data-stu-id="62024-385">Database</span></span>
* <span data-ttu-id="62024-386">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="62024-386">File system</span></span>
* <span data-ttu-id="62024-387">ネットワーク アプライアンス</span><span class="sxs-lookup"><span data-stu-id="62024-387">Network appliances</span></span>
* <span data-ttu-id="62024-388">要求 - 応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="62024-388">Request-response pipeline</span></span>

<span data-ttu-id="62024-389">単体テストでは、インフラストラクチャ コンポーネントの代わりに、*フェイク*または*モック オブジェクト*と呼ばれる、作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-389">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="62024-390">単体テストと比較すると、統合テストは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="62024-390">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="62024-391">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-391">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="62024-392">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="62024-392">Require more code and data processing.</span></span>
* <span data-ttu-id="62024-393">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="62024-393">Take longer to run.</span></span>

<span data-ttu-id="62024-394">そのため、統合テストの使用は最も重要なインフラストラクチャ シナリオに限定します。</span><span class="sxs-lookup"><span data-stu-id="62024-394">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="62024-395">単体テストと統合テストのどちらを使用しても動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="62024-395">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="62024-396">統合テストは、データベースとファイル システムを使用するデータおよびファイル アクセスのすべての順列として記述してはいけません。</span><span class="sxs-lookup"><span data-stu-id="62024-396">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="62024-397">通常は、アプリ全体でデータベースやファイル システムを操作する場所がいくつあったとしても、的を絞った一連の読み取り、書き込み、更新、削除の統合テストを行うことで、データベースおよびファイル システム コンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="62024-397">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="62024-398">これらのコンポーネントと連携するメソッドのロジックのルーチン テストには、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-398">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="62024-399">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="62024-399">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="62024-400">統合テストの説明では、テスト対象のプロジェクトをよく*テスト対象システム*、または短縮して "SUT" と呼びます。</span><span class="sxs-lookup"><span data-stu-id="62024-400">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="62024-401">*このトピック全体で、テスト対象の ASP.NET Core アプリを指すために "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="62024-401">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="62024-402">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="62024-402">ASP.NET Core integration tests</span></span>

<span data-ttu-id="62024-403">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="62024-403">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="62024-404">テスト プロジェクトは、テストを格納して実行するために使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-404">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="62024-405">テスト プロジェクトは SUT への参照を含みます。</span><span class="sxs-lookup"><span data-stu-id="62024-405">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="62024-406">テスト プロジェクトは、SUT のテスト Web ホストを作成し、テスト サーバー クライアントを使用して SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="62024-406">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="62024-407">テスト ランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="62024-407">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="62024-408">統合テストでは、通常の *Arrange (配置)* 、*Act (実行)* 、および *Assert (確認)* のテスト ステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="62024-408">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="62024-409">SUT の Web ホストが構成されます。</span><span class="sxs-lookup"><span data-stu-id="62024-409">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="62024-410">アプリに要求を送信するためのテスト サーバー クライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="62024-410">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="62024-411">*Arrange (配置)* テスト ステップが実行されます。テスト アプリが要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="62024-411">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="62024-412">*Act (実行)* テスト ステップが実行されます。クライアントは要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="62024-412">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="62024-413">*Assert (確認)* テスト ステップが実行されます。*実際*の応答は、*予測される*応答に基づき、*成功*または*失敗*として検証されます。</span><span class="sxs-lookup"><span data-stu-id="62024-413">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="62024-414">このプロセスは、すべてのテストが実行されるまで続行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-414">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="62024-415">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="62024-415">The test results are reported.</span></span>

<span data-ttu-id="62024-416">通常、テスト Web ホストは、アプリの通常のテスト用の Web ホストとは異なる方法で構成されています。</span><span class="sxs-lookup"><span data-stu-id="62024-416">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="62024-417">たとえば、テスト用に別のデータベースまたは異なるアプリ設定を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="62024-417">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="62024-418">テスト Web ホストやメモリ内テスト サーバー ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャ コンポーネントは、[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="62024-418">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="62024-419">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="62024-419">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="62024-420">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="62024-420">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="62024-421">依存関係ファイル ( *.deps*) を SUT からテスト プロジェクトの *bin* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="62024-421">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="62024-422">テストを実行したときに、静的なファイルとページ/ビューが検出されるように、[コンテンツ ルート](xref:fundamentals/index#content-root)を SUT のプロジェクト ルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-422">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="62024-423">[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) クラスを提供し、`TestServer` を使用して SUT のブートストラップを効率化します。</span><span class="sxs-lookup"><span data-stu-id="62024-423">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="62024-424">[単体テストの](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)ドキュメントでは、テスト プロジェクトとテスト ランナーを設定する方法、テストを実行する方法の詳細な手順、テストおよびテスト クラスの命名方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="62024-424">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="62024-425">アプリのテスト プロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="62024-425">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="62024-426">これにより、インフラストラクチャ テスト コンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="62024-426">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="62024-427">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="62024-427">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="62024-428">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いがありません。</span><span class="sxs-lookup"><span data-stu-id="62024-428">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="62024-429">唯一の違いは、テストの命名方法です。</span><span class="sxs-lookup"><span data-stu-id="62024-429">The only difference is in how the tests are named.</span></span> <span data-ttu-id="62024-430">Razor Pages アプリでは、ページ エンドポイントのテストは通常、ページ モデル クラスにちなんだ名前が付けられます (たとえば、`IndexPageTests` では Index ページのコンポーネントの統合テストが行われます)。</span><span class="sxs-lookup"><span data-stu-id="62024-430">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="62024-431">MVC アプリでは、テストは通常、コントローラー クラス別に編成され、テストするコントローラーにちなんだ名前が付けられます (たとえば、`HomeControllerTests` は Home コントローラーのコンポーネントの統合テストを行います)。</span><span class="sxs-lookup"><span data-stu-id="62024-431">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="62024-432">テスト アプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="62024-432">Test app prerequisites</span></span>

<span data-ttu-id="62024-433">テスト プロジェクトは、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-433">The test project must:</span></span>

* <span data-ttu-id="62024-434">次のパッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="62024-434">Reference the following packages:</span></span>
  * [<span data-ttu-id="62024-435">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="62024-435">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="62024-436">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="62024-436">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="62024-437">プロジェクト ファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定しています。</span><span class="sxs-lookup"><span data-stu-id="62024-437">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="62024-438">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照する場合は、Web SDK が必要です。</span><span class="sxs-lookup"><span data-stu-id="62024-438">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="62024-439">これらの前提条件は、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で確認できます。</span><span class="sxs-lookup"><span data-stu-id="62024-439">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="62024-440">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="62024-440">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="62024-441">このサンプル アプリでは、[xUnit](https://xunit.github.io/) テスト フレームワークと [AngleSharp](https://anglesharp.github.io/) パーサー ライブラリを使用するので、サンプル アプリは以下も参照します。</span><span class="sxs-lookup"><span data-stu-id="62024-441">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="62024-442">xunit</span><span class="sxs-lookup"><span data-stu-id="62024-442">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="62024-443">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="62024-443">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="62024-444">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="62024-444">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="62024-445">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="62024-445">SUT environment</span></span>

<span data-ttu-id="62024-446">SUT の [環境](xref:fundamentals/environments) が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="62024-446">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="62024-447">既定の WebApplicationFactory を使用した基本的なテスト</span><span class="sxs-lookup"><span data-stu-id="62024-447">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="62024-448">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) は、統合テスト用の [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-448">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="62024-449">`TEntryPoint` は SUT のエントリ ポイント クラスであり、通常は `Startup` クラスです。</span><span class="sxs-lookup"><span data-stu-id="62024-449">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="62024-450">テスト クラスは、クラスにテストが含まれていることを示すために*クラス フィクスチャ* インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内テストの共有オブジェクト インスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="62024-450">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="62024-451">次のテスト クラス `BasicTests` は、`WebApplicationFactory` を使用して SUT をブートストラップし、テスト メソッド `Get_EndpointsReturnSuccessAndCorrectContentType` に [HttpClient](/dotnet/api/system.net.http.httpclient) を提供します。</span><span class="sxs-lookup"><span data-stu-id="62024-451">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="62024-452">このメソッドは、複数のアプリ ページで応答状態コードが成功かどうか (200-299 の範囲の状態コード) と、`Content-Type` ヘッダーが `text/html; charset=utf-8` であるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="62024-452">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="62024-453">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) は `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトに従い、cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="62024-453">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="62024-454">既定では、[GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、要求間で必須でない cookie は保持されません。</span><span class="sxs-lookup"><span data-stu-id="62024-454">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="62024-455">TempData プロバイダーで使用されているような必須ではない cookie を保持するには、テストに必須であることをマークします。</span><span class="sxs-lookup"><span data-stu-id="62024-455">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="62024-456">cookie を必須としてマークする手順については、[必須 cookie](xref:security/gdpr#essential-cookies) に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62024-456">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="62024-457">WebApplicationFactory のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="62024-457">Customize WebApplicationFactory</span></span>

<span data-ttu-id="62024-458">Web ホストの構成は、`WebApplicationFactory` から継承して 1 つ以上のカスタム ファクトリを作成することで、テスト クラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="62024-458">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="62024-459">`WebApplicationFactory` から継承し、[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost) をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="62024-459">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="62024-460">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) では、[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) を使用してサービス コレクションを構成できます。これはアプリの `Startup.ConfigureServices` の前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-460">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="62024-461">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) では、[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) を使用して、サービス コレクション内の登録済みサービスをオーバーライドしたり変更したりできます。</span><span class="sxs-lookup"><span data-stu-id="62024-461">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="62024-462">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-462">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="62024-463">このメソッドについては、[統合テストのサンプル: テスト アプリの構成](#test-app-organization)に関するセクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62024-463">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="62024-464">テスト クラスでカスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-464">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="62024-465">次の例では、`IndexPageTests` クラスでファクトリを使用しています。</span><span class="sxs-lookup"><span data-stu-id="62024-465">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="62024-466">サンプル アプリのクライアントは、`HttpClient` がリダイレクトに従わないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="62024-466">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="62024-467">[モック認証](#mock-authentication)のセクションで後述するように、これによってアプリの最初の応答結果を確認するテストが可能になります。</span><span class="sxs-lookup"><span data-stu-id="62024-467">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="62024-468">これらのテストでは、多くの場合、最初の応答は `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="62024-468">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="62024-469">一般的なテストでは、`HttpClient` およびヘルパー メソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="62024-469">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="62024-470">SUT に対する POST 要求は、アプリの[偽造防止データ保護システム](xref:security/data-protection/introduction)によって自動的に行われる偽造防止チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-470">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="62024-471">テストで POST 要求を実行するには、テスト アプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-471">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="62024-472">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="62024-472">Make a request for the page.</span></span>
1. <span data-ttu-id="62024-473">応答の偽造防止 cookie と要求検証トークンを解析します。</span><span class="sxs-lookup"><span data-stu-id="62024-473">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="62024-474">偽造防止 cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="62024-474">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="62024-475">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパー メソッド (*Helpers/HtmlHelpers.cs*) は、次のメソッドで [AngleSharp](https://anglesharp.github.io/) パーサーを使用して偽造防止チェック処理を行います。</span><span class="sxs-lookup"><span data-stu-id="62024-475">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="62024-476">`GetDocumentAsync`:[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="62024-476">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="62024-477">`GetDocumentAsync` は、元の `HttpResponseMessage` に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-477">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="62024-478">詳しくは、[AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62024-478">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="62024-479">`HttpClient` の `SendAsync` 拡張メソッドは、[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) を作成し、[SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="62024-479">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="62024-480">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="62024-480">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="62024-481">フォームの送信ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="62024-481">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="62024-482">フォームの値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="62024-482">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="62024-483">送信ボタン (`IHtmlElement`) とフォームの値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="62024-483">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="62024-484">[AngleSharp](https://anglesharp.github.io/) は、このトピックとサンプル アプリのデモンストレーションのために使用するサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="62024-484">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="62024-485">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="62024-485">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="62024-486">[Html Agility Pack (HAP)](https://html-agility-pack.net/) などの他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="62024-486">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="62024-487">もう 1 つの方法として、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造防止 cookie を直接処理する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="62024-487">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="62024-488">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="62024-488">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="62024-489">テスト メソッド内で追加の構成が必要な場合は、[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) が構成によってさらにカスタマイズされた [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) を使用して新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="62024-489">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="62024-490">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テスト メソッドは、`WithWebHostBuilder` の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="62024-490">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="62024-491">このテストでは、SUT からフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="62024-491">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="62024-492">`IndexPageTests` クラス内の別のテストは、データベース内のすべてのレコードを削除する操作を実行します。この操作が `Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行される可能性があるため、このテスト メソッド内でデータベースの再シード処理を行い、確実に SUT が削除するレコードが存在するようにしています。</span><span class="sxs-lookup"><span data-stu-id="62024-492">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="62024-493">SUT に対する要求では、SUT の `messages` フォームの最初の [削除] ボタンの選択がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="62024-493">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="62024-494">クライアントのオプション</span><span class="sxs-lookup"><span data-stu-id="62024-494">Client options</span></span>

<span data-ttu-id="62024-495">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示します。</span><span class="sxs-lookup"><span data-stu-id="62024-495">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="62024-496">オプション</span><span class="sxs-lookup"><span data-stu-id="62024-496">Option</span></span> | <span data-ttu-id="62024-497">説明</span><span class="sxs-lookup"><span data-stu-id="62024-497">Description</span></span> | <span data-ttu-id="62024-498">Default</span><span class="sxs-lookup"><span data-stu-id="62024-498">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="62024-499">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="62024-499">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="62024-500">`HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-500">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="62024-501">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="62024-501">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="62024-502">`HttpClient` インスタンスのベース アドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-502">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| <span data-ttu-id="62024-503">[HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)</span><span class="sxs-lookup"><span data-stu-id="62024-503">[HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)</span></span> | <span data-ttu-id="62024-504">`HttpClient` インスタンスが cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-504">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="62024-505">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="62024-505">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="62024-506">`HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-506">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="62024-507">7</span><span class="sxs-lookup"><span data-stu-id="62024-507">7</span></span> |

<span data-ttu-id="62024-508">`WebApplicationFactoryClientOptions` クラスを作成し、それを [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="62024-508">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="62024-509">モック サービスの注入</span><span class="sxs-lookup"><span data-stu-id="62024-509">Inject mock services</span></span>

<span data-ttu-id="62024-510">ホスト ビルダーで [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) を呼び出すと、テストでサービスをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="62024-510">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="62024-511">**モック サービスを注入するには、SUT に `Startup` クラスが存在し、そこに `Startup.ConfigureServices` メソッドが存在している必要があります。**</span><span class="sxs-lookup"><span data-stu-id="62024-511">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="62024-512">サンプルの SUT には、引用符を返すスコープ サービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="62024-512">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="62024-513">インデックス ページが要求されると、インデックス ページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="62024-513">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="62024-514">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-514">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="62024-515">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-515">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="62024-516">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-516">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="62024-517">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-517">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="62024-518">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-518">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="62024-519">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="62024-519">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="62024-520">統合テストでサービスと引用符の注入をテストするため、テストは SUT にモック サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="62024-520">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="62024-521">モック サービスは、アプリの `QuoteService` をテスト アプリが提供する `TestQuoteService` と呼ばれるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="62024-521">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="62024-522">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="62024-522">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="62024-523">`ConfigureTestServices` が呼び出され、スコープ サービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="62024-523">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="62024-524">`TestQuoteService` によって指定された引用符テキストがテストの実行中に生成されたマークアップに反映されるため、アサーションは成功します。</span><span class="sxs-lookup"><span data-stu-id="62024-524">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="62024-525">モック認証</span><span class="sxs-lookup"><span data-stu-id="62024-525">Mock authentication</span></span>

<span data-ttu-id="62024-526">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="62024-526">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="62024-527">認証されていないユーザーは、アプリのログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="62024-527">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="62024-528">認証されたユーザーには、コンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="62024-528">Returns content for an authenticated user.</span></span>

<span data-ttu-id="62024-529">SUT の `/SecurePage` ページは、[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 規約を使用してページに [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) を適用します。</span><span class="sxs-lookup"><span data-stu-id="62024-529">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="62024-530">詳細については、[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-530">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="62024-531">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) を `false` に設定することで、[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) がリダイレクトを許可しないように設定しています。</span><span class="sxs-lookup"><span data-stu-id="62024-531">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="62024-532">クライアントがリダイレクトに従うことを許可しないことで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="62024-532">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="62024-533">ログイン ページにリダイレクトした後の最終的な状態コード (これは [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode) になります) ではなく、予期される [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) の結果を SUT が返すステータス コードと突き合わせて確認できます。</span><span class="sxs-lookup"><span data-stu-id="62024-533">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="62024-534">最終的なログイン ページ応答 (ここでは、`Location` ヘッダーは存在しません) ではなく、応答ヘッダーの `Location` ヘッダー値が `http://localhost/Identity/Account/Login` で始まることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="62024-534">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="62024-535">テスト アプリでは、認証と承認の側面をテストするために [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="62024-535">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="62024-536">最小のシナリオでは、[AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="62024-536">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="62024-537">`TestAuthHandler` は、認証スキームが `Test` に設定されている場合 (この場合、`AddAuthentication` が `ConfigureTestServices` に登録されています) に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="62024-537">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="62024-538">`WebApplicationFactoryClientOptions` の詳細については、「[クライアントのオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-538">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="62024-539">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="62024-539">Set the environment</span></span>

<span data-ttu-id="62024-540">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="62024-540">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="62024-541">SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="62024-541">To override the SUT's environment:</span></span>

* <span data-ttu-id="62024-542">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing` などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-542">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="62024-543">テスト アプリで `CreateWebHostBuilder` をオーバーライドして、`ASPNETCORE_ENVIRONMENT` 環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="62024-543">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="62024-544">環境は、カスタムの <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> のホスト ビルダーで直接設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="62024-544">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="62024-545">テスト インフラストラクチャがアプリ コンテンツのルート パスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="62024-545">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="62024-546">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリの `System.Reflection.Assembly.FullName` と同じキーを持つ統合テストを含むアセンブリで [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) を検索することによって、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パスを推測します。</span><span class="sxs-lookup"><span data-stu-id="62024-546">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="62024-547">正しいキーを持つ属性が見つからない場合、`WebApplicationFactory` はフォールバックしてソリューション ファイル ( *.sln*) を検索し、`TEntryPoint` アセンブリ名をソリューション ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="62024-547">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="62024-548">アプリのルート ディレクトリ (コンテンツ ルート パス) は、ビューやコンテンツのファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="62024-548">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="62024-549">シャドウ コピーの無効化</span><span class="sxs-lookup"><span data-stu-id="62024-549">Disable shadow copying</span></span>

<span data-ttu-id="62024-550">シャドウ コピーを行うと、テストが出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-550">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="62024-551">テストが適切に機能するように、シャドウ コピーを無効化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="62024-551">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="62024-552">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では、xUnit を使用して正しい構成設定の *xunit.runner.json* ファイルを含めることにより、xUnit のシャドウ コピーを無効化しています。</span><span class="sxs-lookup"><span data-stu-id="62024-552">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="62024-553">詳しくは、[JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62024-553">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="62024-554">次の内容を使用して、テスト プロジェクトのルートに *xunit.runner.json* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="62024-554">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="62024-555">Visual Studio を使用する場合は、ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピーする]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="62024-555">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="62024-556">Visual Studio を使用しない場合は、テスト アプリのプロジェクト ファイルに `Content` ターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="62024-556">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="62024-557">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="62024-557">Disposal of objects</span></span>

<span data-ttu-id="62024-558">`IClassFixture` 実装のテストを実行した後、xUnit が [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) を破棄すると、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) と [HttpClient](/dotnet/api/system.net.http.httpclient) が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="62024-558">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="62024-559">開発者がインスタンス化したオブジェクトを破棄する必要がある場合は、`IClassFixture` の実装で破棄します。</span><span class="sxs-lookup"><span data-stu-id="62024-559">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="62024-560">詳細については、「[Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="62024-560">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="62024-561">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="62024-561">Integration tests sample</span></span>

<span data-ttu-id="62024-562">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="62024-562">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="62024-563">アプリ</span><span class="sxs-lookup"><span data-stu-id="62024-563">App</span></span> | <span data-ttu-id="62024-564">プロジェクト ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="62024-564">Project directory</span></span> | <span data-ttu-id="62024-565">説明</span><span class="sxs-lookup"><span data-stu-id="62024-565">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="62024-566">メッセージ アプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="62024-566">Message app (the SUT)</span></span> | <span data-ttu-id="62024-567">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="62024-567">*src/RazorPagesProject*</span></span> | <span data-ttu-id="62024-568">ユーザーは、メッセージの追加、1 つ削除、すべて削除、および分析を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="62024-568">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="62024-569">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="62024-569">Test app</span></span> | <span data-ttu-id="62024-570">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="62024-570">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="62024-571">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="62024-571">Used to integration test the SUT.</span></span> |

<span data-ttu-id="62024-572">テストは、[Visual Studio](https://visualstudio.microsoft.com) などの IDE に組み込まれているテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="62024-572">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="62024-573">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用している場合は、コマンド プロンプトで *tests/RazorPagesProject.Tests* ディレクトリを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="62024-573">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="62024-574">メッセージ アプリ (SUT) の構成</span><span class="sxs-lookup"><span data-stu-id="62024-574">Message app (SUT) organization</span></span>

<span data-ttu-id="62024-575">SUT は、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="62024-575">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="62024-576">アプリのインデックス ページ (*Pages/Index.cshtml* と *Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="62024-576">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="62024-577">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="62024-577">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="62024-578">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="62024-578">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="62024-579">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="62024-579">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="62024-580">アプリのデータベース コンテキスト クラスである `AppDbContext` (*Data/AppDbContext.cs*) には、データアクセス層 (DAL) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="62024-580">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="62024-581">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="62024-581">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="62024-582">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="62024-582">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="62024-583">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="62024-583">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="62024-584">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="62024-584">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="62024-585">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="62024-585">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="62024-586">アプリはリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="62024-586">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="62024-587">詳細については、[インフラストラクチャの永続化レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)および[コントローラー ロジックのテスト](/aspnet/core/mvc/controllers/testing)に関する記事を参照してください (このサンプルはリポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="62024-587">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="62024-588">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="62024-588">Test app organization</span></span>

<span data-ttu-id="62024-589">テスト アプリは、*tests/RazorPagesProject.Tests* ディレクトリにあるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="62024-589">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="62024-590">テスト アプリのディレクトリ</span><span class="sxs-lookup"><span data-stu-id="62024-590">Test app directory</span></span> | <span data-ttu-id="62024-591">説明</span><span class="sxs-lookup"><span data-stu-id="62024-591">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="62024-592">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="62024-592">*AuthTests*</span></span> | <span data-ttu-id="62024-593">次のテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="62024-593">Contains test methods for:</span></span><ul><li><span data-ttu-id="62024-594">認証されていないユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="62024-594">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="62024-595">モック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> で認証されたユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="62024-595">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="62024-596">GitHub ユーザー プロファイルを取得し、プロファイルのユーザー ログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="62024-596">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="62024-597">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="62024-597">*BasicTests*</span></span> | <span data-ttu-id="62024-598">ルーティングおよびコンテンツ タイプのテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="62024-598">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="62024-599">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="62024-599">*IntegrationTests*</span></span> | <span data-ttu-id="62024-600">カスタム `WebApplicationFactory` クラスを使用したインデックス ページの統合テストを含みます。</span><span class="sxs-lookup"><span data-stu-id="62024-600">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="62024-601">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="62024-601">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="62024-602">*Utilities.cs* には、データベースにテスト データをシードする `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="62024-602">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="62024-603">*HtmlHelpers.cs* には、テスト メソッドで使用する AngleSharp `IHtmlDocument` を返すメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="62024-603">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="62024-604">*HttpClientExtensions.cs* は、SUT に要求を送信する `SendAsync` のオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="62024-604">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="62024-605">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="62024-605">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="62024-606">統合テストは、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を含む [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="62024-606">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="62024-607">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージはテスト ホストとテスト サーバーを構成するために使用されるため、テスト アプリのプロジェクト ファイルまたはテスト アプリの開発者構成で直接 `TestHost` および `TestServer` パッケージを参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="62024-607">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="62024-608">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="62024-608">**Seeding the database for testing**</span></span>

<span data-ttu-id="62024-609">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="62024-609">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="62024-610">たとえば、削除テストでは、データベース レコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも 1 つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="62024-610">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="62024-611">このサンプル アプリでは、*Utilities.cs* で 3 つのメッセージを使用してデータベースをシードします。このメッセージは、テストを実行する際に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="62024-611">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="62024-612">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="62024-612">Additional resources</span></span>

* [<span data-ttu-id="62024-613">単体テスト</span><span class="sxs-lookup"><span data-stu-id="62024-613">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
