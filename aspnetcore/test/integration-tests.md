---
title: ASP.NET Core での統合テスト
author: rick-anderson
description: 統合テストによってデータベース、ファイル システム、ネットワークなどのインフラストラクチャ レベルで、アプリのコンポーネントがどのように正しく機能するようになるかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/integration-tests
ms.openlocfilehash: ee57843dc79805aed48dbb4d3a6ac97469052f5c
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105182"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="67a3b-103">ASP.NET Core での統合テスト</span><span class="sxs-lookup"><span data-stu-id="67a3b-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="67a3b-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Steve Smith](https://ardalis.com/)、[Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="67a3b-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67a3b-105">統合テストでは、データベース、ファイル システム、ネットワークなど、アプリのサポート インフラストラクチャを含むレベルで、アプリのコンポーネントが正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="67a3b-106">ASP.NET Core では、単体テスト フレームワークとテスト Web ホストおよびメモリ内テスト サーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="67a3b-107">このトピックを読むには、単体テストの基本的な知識があることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="67a3b-108">テストの概念を理解していない場合は、「[.NET Core の単体テストと .NET Standard」](/dotnet/core/testing/) のトピックとリンクされたコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="67a3b-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="67a3b-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="67a3b-110">このサンプル アプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="67a3b-111">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="67a3b-112">[Razor Pages の概要](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="67a3b-112">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="67a3b-113">[Razor Pages の使用を開始する](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="67a3b-113">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="67a3b-114">[Razor Pages の単体テスト](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="67a3b-114">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="67a3b-115">SPA をテストする場合は、ブラウザーを自動化できる [Selenium](https://www.seleniumhq.org/) などのツールを推奨します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="67a3b-116">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="67a3b-116">Introduction to integration tests</span></span>

<span data-ttu-id="67a3b-117">統合テストでは [単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="67a3b-118">単体テストは、個々のクラス メソッドなど、分離されたソフトウェア コンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="67a3b-119">統合テストでは、2 つ以上のアプリ コンポーネントが、連携して予想される結果を生成することを確認します。これは、要求を完全に処理するために必要なすべてのコンポーネントを含む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="67a3b-120">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="67a3b-121">データベース</span><span class="sxs-lookup"><span data-stu-id="67a3b-121">Database</span></span>
* <span data-ttu-id="67a3b-122">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="67a3b-122">File system</span></span>
* <span data-ttu-id="67a3b-123">ネットワーク アプライアンス</span><span class="sxs-lookup"><span data-stu-id="67a3b-123">Network appliances</span></span>
* <span data-ttu-id="67a3b-124">要求 - 応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="67a3b-124">Request-response pipeline</span></span>

<span data-ttu-id="67a3b-125">単体テストでは、インフラストラクチャ コンポーネントの代わりに、*フェイク*または*モック オブジェクト*と呼ばれる、作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="67a3b-126">単体テストと比較すると、統合テストは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="67a3b-127">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="67a3b-128">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-128">Require more code and data processing.</span></span>
* <span data-ttu-id="67a3b-129">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-129">Take longer to run.</span></span>

<span data-ttu-id="67a3b-130">そのため、統合テストの使用は最も重要なインフラストラクチャ シナリオに限定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="67a3b-131">単体テストと統合テストのどちらを使用しても動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="67a3b-132">統合テストは、データベースとファイル システムを使用するデータおよびファイル アクセスのすべての順列として記述してはいけません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="67a3b-133">通常は、アプリ全体でデータベースやファイル システムを操作する場所がいくつあったとしても、的を絞った一連の読み取り、書き込み、更新、削除の統合テストを行うことで、データベースおよびファイル システム コンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="67a3b-134">これらのコンポーネントと連携するメソッドのロジックのルーチン テストには、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="67a3b-135">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="67a3b-136">統合テストの説明では、テスト対象のプロジェクトをよく*テスト対象システム*、または短縮して "SUT" と呼びます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="67a3b-137">*このトピック全体で、テスト対象の ASP.NET Core アプリを指すために "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="67a3b-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="67a3b-138">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="67a3b-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="67a3b-139">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="67a3b-140">テスト プロジェクトは、テストを格納して実行するために使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="67a3b-141">テスト プロジェクトは SUT への参照を含みます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="67a3b-142">テスト プロジェクトは、SUT のテスト Web ホストを作成し、テスト サーバー クライアントを使用して SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="67a3b-143">テスト ランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="67a3b-144">統合テストでは、通常の *Arrange (配置)* 、*Act (実行)* 、および *Assert (確認)* のテスト ステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="67a3b-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="67a3b-145">SUT の Web ホストが構成されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="67a3b-146">アプリに要求を送信するためのテスト サーバー クライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="67a3b-147">*Arrange (配置)* テスト ステップが実行されます。テスト アプリが要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="67a3b-148">*Act (実行)* テスト ステップが実行されます。クライアントは要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="67a3b-149">*Assert (確認)* テスト ステップが実行されます。*実際*の応答は、*予測される*応答に基づき、*成功*または*失敗*として検証されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="67a3b-150">このプロセスは、すべてのテストが実行されるまで続行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="67a3b-151">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-151">The test results are reported.</span></span>

<span data-ttu-id="67a3b-152">通常、テスト Web ホストは、アプリの通常のテスト用の Web ホストとは異なる方法で構成されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="67a3b-153">たとえば、テスト用に別のデータベースまたは異なるアプリ設定を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="67a3b-154">テスト Web ホストやメモリ内テスト サーバー ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャ コンポーネントは、[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="67a3b-155">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="67a3b-156">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="67a3b-157">依存関係ファイル ( *.deps*) を SUT からテスト プロジェクトの *bin* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="67a3b-158">テストを実行したときに、静的なファイルとページ/ビューが検出されるように、[コンテンツ ルート](xref:fundamentals/index#content-root)を SUT のプロジェクト ルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="67a3b-159">[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) クラスを提供し、`TestServer` を使用して SUT のブートストラップを効率化します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="67a3b-160">[単体テストの](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)ドキュメントでは、テスト プロジェクトとテスト ランナーを設定する方法、テストを実行する方法の詳細な手順、テストおよびテスト クラスの命名方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="67a3b-161">アプリのテスト プロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="67a3b-162">これにより、インフラストラクチャ テスト コンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="67a3b-163">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="67a3b-164">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いがありません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="67a3b-165">唯一の違いは、テストの命名方法です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="67a3b-166">Razor Pages アプリでは、ページ エンドポイントのテストは通常、ページ モデル クラスにちなんだ名前が付けられます (たとえば、`IndexPageTests` では Index ページのコンポーネントの統合テストが行われます)。</span><span class="sxs-lookup"><span data-stu-id="67a3b-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="67a3b-167">MVC アプリでは、テストは通常、コントローラー クラス別に編成され、テストするコントローラーにちなんだ名前が付けられます (たとえば、`HomeControllerTests` は Home コントローラーのコンポーネントの統合テストを行います)。</span><span class="sxs-lookup"><span data-stu-id="67a3b-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="67a3b-168">テスト アプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="67a3b-168">Test app prerequisites</span></span>

<span data-ttu-id="67a3b-169">テスト プロジェクトは、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-169">The test project must:</span></span>

* <span data-ttu-id="67a3b-170">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージを参照しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="67a3b-171">プロジェクト ファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="67a3b-172">これらの前提条件は、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で確認できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-172">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="67a3b-173">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* ファイルを確認してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="67a3b-174">このサンプル アプリでは、[xUnit](https://xunit.github.io/) テスト フレームワークと [AngleSharp](https://anglesharp.github.io/) パーサー ライブラリを使用するので、サンプル アプリは以下も参照します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="67a3b-175">xunit</span><span class="sxs-lookup"><span data-stu-id="67a3b-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="67a3b-176">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="67a3b-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="67a3b-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="67a3b-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="67a3b-178">テストでは Entity Framework Core も使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="67a3b-179">アプリは以下を参照します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-179">The app references:</span></span>

* [<span data-ttu-id="67a3b-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="67a3b-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="67a3b-181">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="67a3b-181">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="67a3b-182">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="67a3b-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="67a3b-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="67a3b-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="67a3b-184">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="67a3b-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="67a3b-185">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="67a3b-185">SUT environment</span></span>

<span data-ttu-id="67a3b-186">SUT の [環境](xref:fundamentals/environments) が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="67a3b-187">既定の WebApplicationFactory を使用した基本的なテスト</span><span class="sxs-lookup"><span data-stu-id="67a3b-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="67a3b-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) は、統合テスト用の [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="67a3b-189">`TEntryPoint` は SUT のエントリ ポイント クラスであり、通常は `Startup` クラスです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="67a3b-190">テスト クラスは、クラスにテストが含まれていることを示すために*クラス フィクスチャ* インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内テストの共有オブジェクト インスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="67a3b-191">次のテスト クラス `BasicTests` は、`WebApplicationFactory` を使用して SUT をブートストラップし、テスト メソッド `Get_EndpointsReturnSuccessAndCorrectContentType` に [HttpClient](/dotnet/api/system.net.http.httpclient) を提供します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-191">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="67a3b-192">このメソッドは、複数のアプリ ページで応答状態コードが成功かどうか (200-299 の範囲の状態コード) と、`Content-Type` ヘッダーが `text/html; charset=utf-8` であるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-192">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="67a3b-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) は `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトに従い、Cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="67a3b-194">既定では、[GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、要求間で必須でない Cookie は保持されません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-194">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="67a3b-195">TempData プロバイダーで使用されているような必須ではない Cookie を保持するには、テストに必須であることをマークします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-195">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="67a3b-196">Cookie を必須としてマークする手順については、[必須 Cookie](xref:security/gdpr#essential-cookies) に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-196">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="67a3b-197">WebApplicationFactory のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="67a3b-197">Customize WebApplicationFactory</span></span>

<span data-ttu-id="67a3b-198">Web ホストの構成は、`WebApplicationFactory` から継承して 1 つ以上のカスタム ファクトリを作成することで、テスト クラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-198">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="67a3b-199">`WebApplicationFactory` から継承し、[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost) をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-199">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="67a3b-200">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) では、[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) を使用してサービス コレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-200">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="67a3b-201">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-201">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="67a3b-202">このメソッドについては、[統合テストのサンプル: テスト アプリの構成](#test-app-organization)に関するセクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-202">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="67a3b-203">SUT のデータベース コンテキストは、`Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-203">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="67a3b-204">テスト アプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行された*後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-204">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="67a3b-205">ASP.NET Core 3.0 リリースの[汎用ホスト](xref:fundamentals/host/generic-host)により、実行順序に関する互換性のない変更が行われています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-205">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="67a3b-206">アプリのデータベースとは異なるデータベースをテストに使用するには、`builder.ConfigureServices` でアプリのデータベース コンテキストを置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-206">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="67a3b-207">まだ [Web ホスト](xref:fundamentals/host/web-host)を使用している SUT の場合、テスト アプリの `builder.ConfigureServices` コールバックは、SUT の `Startup.ConfigureServices` コードの "*前に*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-207">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="67a3b-208">テスト アプリの `builder.ConfigureTestServices` コールバックは、"*後で*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-208">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="67a3b-209">サンプル アプリでは、データベース コンテキストのサービス記述子を検索し、記述子を使用してサービス登録を削除しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-209">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="67a3b-210">次に、ファクトリは、テストにメモリ内データベースを使用する新しい `ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-210">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="67a3b-211">メモリ内データベースではないデータベースに接続するには、`UseInMemoryDatabase` 呼び出しを変更して、コンテキストを別のデータベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-211">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="67a3b-212">SQL Server テスト データベースを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-212">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="67a3b-213">プロジェクト ファイルで [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-213">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="67a3b-214">データベースへの接続文字列を使用して `UseSqlServer` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-214">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="67a3b-215">テスト クラスでカスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-215">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="67a3b-216">次の例では、`IndexPageTests` クラスでファクトリを使用しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-216">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="67a3b-217">サンプル アプリのクライアントは、`HttpClient` がリダイレクトに従わないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-217">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="67a3b-218">[モック認証](#mock-authentication)のセクションで後述するように、これによってアプリの最初の応答結果を確認するテストが可能になります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-218">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="67a3b-219">これらのテストでは、多くの場合、最初の応答は `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-219">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="67a3b-220">一般的なテストでは、`HttpClient` およびヘルパー メソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-220">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="67a3b-221">SUT に対する POST 要求は、アプリの[偽造防止データ保護システム](xref:security/data-protection/introduction)によって自動的に行われる偽造防止チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-221">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="67a3b-222">テストで POST 要求を実行するには、テスト アプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-222">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="67a3b-223">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="67a3b-223">Make a request for the page.</span></span>
1. <span data-ttu-id="67a3b-224">応答の偽造防止 Cookie と要求検証トークンを解析します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-224">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="67a3b-225">偽造防止 Cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="67a3b-225">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="67a3b-226">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパー メソッド (*Helpers/HtmlHelpers.cs*) は、次のメソッドで [AngleSharp](https://anglesharp.github.io/) パーサーを使用して偽造防止チェック処理を行います。</span><span class="sxs-lookup"><span data-stu-id="67a3b-226">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="67a3b-227">`GetDocumentAsync`:[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-227">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="67a3b-228">`GetDocumentAsync` は、元の `HttpResponseMessage` に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-228">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="67a3b-229">詳しくは、[AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-229">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="67a3b-230">`HttpClient` の `SendAsync` 拡張メソッドは、[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) を作成し、[SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-230">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="67a3b-231">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-231">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="67a3b-232">フォームの送信ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="67a3b-232">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="67a3b-233">フォームの値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="67a3b-233">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="67a3b-234">送信ボタン (`IHtmlElement`) とフォームの値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="67a3b-234">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="67a3b-235">[AngleSharp](https://anglesharp.github.io/) は、このトピックとサンプル アプリのデモンストレーションのために使用するサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-235">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="67a3b-236">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-236">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="67a3b-237">[Html Agility Pack (HAP)](https://html-agility-pack.net/) などの他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-237">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="67a3b-238">もう 1 つの方法として、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造防止 Cookie を直接処理する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-238">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="67a3b-239">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="67a3b-239">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="67a3b-240">テスト メソッド内で追加の構成が必要な場合は、[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) が構成によってさらにカスタマイズされた [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) を使用して新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-240">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="67a3b-241">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テスト メソッドは、`WithWebHostBuilder` の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-241">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="67a3b-242">このテストでは、SUT からフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-242">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="67a3b-243">`IndexPageTests` クラス内の別のテストは、データベース内のすべてのレコードを削除する操作を実行します。この操作が `Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行される可能性があるため、このテスト メソッド内でデータベースの再シード処理を行い、確実に SUT が削除するレコードが存在するようにしています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-243">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="67a3b-244">SUT に対する要求では、SUT の `messages` フォームの最初の [削除] ボタンの選択がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-244">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="67a3b-245">クライアントのオプション</span><span class="sxs-lookup"><span data-stu-id="67a3b-245">Client options</span></span>

<span data-ttu-id="67a3b-246">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-246">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="67a3b-247">オプション</span><span class="sxs-lookup"><span data-stu-id="67a3b-247">Option</span></span> | <span data-ttu-id="67a3b-248">説明</span><span class="sxs-lookup"><span data-stu-id="67a3b-248">Description</span></span> | <span data-ttu-id="67a3b-249">Default</span><span class="sxs-lookup"><span data-stu-id="67a3b-249">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="67a3b-250">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="67a3b-250">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="67a3b-251">`HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-251">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="67a3b-252">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="67a3b-252">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="67a3b-253">`HttpClient` インスタンスのベース アドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-253">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="67a3b-254">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="67a3b-254">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="67a3b-255">`HttpClient` インスタンスが Cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-255">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="67a3b-256">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="67a3b-256">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="67a3b-257">`HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-257">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="67a3b-258">7</span><span class="sxs-lookup"><span data-stu-id="67a3b-258">7</span></span> |

<span data-ttu-id="67a3b-259">`WebApplicationFactoryClientOptions` クラスを作成し、それを [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="67a3b-259">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="67a3b-260">モック サービスの注入</span><span class="sxs-lookup"><span data-stu-id="67a3b-260">Inject mock services</span></span>

<span data-ttu-id="67a3b-261">ホスト ビルダーで [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) を呼び出すと、テストでサービスをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-261">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="67a3b-262">**モック サービスを注入するには、SUT に `Startup` クラスが存在し、そこに `Startup.ConfigureServices` メソッドが存在している必要があります。**</span><span class="sxs-lookup"><span data-stu-id="67a3b-262">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="67a3b-263">サンプルの SUT には、引用符を返すスコープ サービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-263">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="67a3b-264">インデックス ページが要求されると、インデックス ページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-264">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="67a3b-265">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-265">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="67a3b-266">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-266">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="67a3b-267">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-267">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="67a3b-268">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-268">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="67a3b-269">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-269">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="67a3b-270">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-270">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="67a3b-271">統合テストでサービスと引用符の注入をテストするため、テストは SUT にモック サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-271">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="67a3b-272">モック サービスは、アプリの `QuoteService` をテスト アプリが提供する `TestQuoteService` と呼ばれるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-272">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="67a3b-273">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-273">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="67a3b-274">`ConfigureTestServices` が呼び出され、スコープ サービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-274">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="67a3b-275">`TestQuoteService` によって指定された引用符テキストがテストの実行中に生成されたマークアップに反映されるため、アサーションは成功します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-275">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="67a3b-276">モック認証</span><span class="sxs-lookup"><span data-stu-id="67a3b-276">Mock authentication</span></span>

<span data-ttu-id="67a3b-277">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-277">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="67a3b-278">認証されていないユーザーは、アプリのログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-278">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="67a3b-279">認証されたユーザーには、コンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-279">Returns content for an authenticated user.</span></span>

<span data-ttu-id="67a3b-280">SUT の `/SecurePage` ページは、[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 規約を使用してページに [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) を適用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-280">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="67a3b-281">詳細については、[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-281">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="67a3b-282">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) を `false` に設定することで、[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) がリダイレクトを許可しないように設定しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-282">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="67a3b-283">クライアントがリダイレクトに従うことを許可しないことで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-283">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="67a3b-284">ログイン ページにリダイレクトした後の最終的な状態コード (これは [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode) になります) ではなく、予期される [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) の結果を SUT が返すステータス コードと突き合わせて確認できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-284">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="67a3b-285">最終的なログイン ページ応答 (ここでは、`Location` ヘッダーは存在しません) ではなく、応答ヘッダーの `Location` ヘッダー値が `http://localhost/Identity/Account/Login` で始まることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-285">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="67a3b-286">テスト アプリでは、認証と承認の側面をテストするために [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-286">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="67a3b-287">最小のシナリオでは、[AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-287">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="67a3b-288">`TestAuthHandler` は、認証スキームが `Test` に設定されている場合 (この場合、`AddAuthentication` が `ConfigureTestServices` に登録されています) に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-288">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="67a3b-289">`WebApplicationFactoryClientOptions` の詳細については、「[クライアントのオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-289">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="67a3b-290">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="67a3b-290">Set the environment</span></span>

<span data-ttu-id="67a3b-291">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-291">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="67a3b-292">`IHostBuilder` を使用しているときに SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-292">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="67a3b-293">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing` などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-293">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="67a3b-294">テスト アプリで `CreateHostBuilder` をオーバーライドして、`ASPNETCORE` で始まる環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-294">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="67a3b-295">SUT が Web ホスト (`IWebHostBuilder`) を使用している場合は、`CreateWebHostBuilder` をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-295">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="67a3b-296">テスト インフラストラクチャがアプリ コンテンツのルート パスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="67a3b-296">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="67a3b-297">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリの `System.Reflection.Assembly.FullName` と同じキーを持つ統合テストを含むアセンブリで [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) を検索することによって、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パスを推測します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-297">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="67a3b-298">正しいキーを持つ属性が見つからない場合、`WebApplicationFactory` はフォールバックしてソリューション ファイル ( *.sln*) を検索し、`TEntryPoint` アセンブリ名をソリューション ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-298">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="67a3b-299">アプリのルート ディレクトリ (コンテンツ ルート パス) は、ビューやコンテンツのファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-299">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="67a3b-300">シャドウ コピーの無効化</span><span class="sxs-lookup"><span data-stu-id="67a3b-300">Disable shadow copying</span></span>

<span data-ttu-id="67a3b-301">シャドウ コピーを行うと、テストが出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-301">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="67a3b-302">テストが適切に機能するように、シャドウ コピーを無効化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-302">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="67a3b-303">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では、xUnit を使用して正しい構成設定の *xunit.runner.json* ファイルを含めることにより、xUnit のシャドウ コピーを無効化しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-303">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="67a3b-304">詳しくは、[JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-304">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="67a3b-305">次の内容を使用して、テスト プロジェクトのルートに *xunit.runner.json* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-305">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="67a3b-306">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="67a3b-306">Disposal of objects</span></span>

<span data-ttu-id="67a3b-307">`IClassFixture` 実装のテストを実行した後、xUnit が [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) を破棄すると、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) と [HttpClient](/dotnet/api/system.net.http.httpclient) が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-307">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="67a3b-308">開発者がインスタンス化したオブジェクトを破棄する必要がある場合は、`IClassFixture` の実装で破棄します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-308">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="67a3b-309">詳細については、「[Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-309">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="67a3b-310">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="67a3b-310">Integration tests sample</span></span>

<span data-ttu-id="67a3b-311">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-311">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="67a3b-312">アプリ</span><span class="sxs-lookup"><span data-stu-id="67a3b-312">App</span></span> | <span data-ttu-id="67a3b-313">プロジェクト ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="67a3b-313">Project directory</span></span> | <span data-ttu-id="67a3b-314">説明</span><span class="sxs-lookup"><span data-stu-id="67a3b-314">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="67a3b-315">メッセージ アプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="67a3b-315">Message app (the SUT)</span></span> | <span data-ttu-id="67a3b-316">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="67a3b-316">*src/RazorPagesProject*</span></span> | <span data-ttu-id="67a3b-317">ユーザーは、メッセージの追加、1 つ削除、すべて削除、および分析を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-317">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="67a3b-318">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="67a3b-318">Test app</span></span> | <span data-ttu-id="67a3b-319">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="67a3b-319">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="67a3b-320">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-320">Used to integration test the SUT.</span></span> |

<span data-ttu-id="67a3b-321">テストは、[Visual Studio](https://visualstudio.microsoft.com) などの IDE に組み込まれているテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-321">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="67a3b-322">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用している場合は、コマンド プロンプトで *tests/RazorPagesProject.Tests* ディレクトリを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-322">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="67a3b-323">メッセージ アプリ (SUT) の構成</span><span class="sxs-lookup"><span data-stu-id="67a3b-323">Message app (SUT) organization</span></span>

<span data-ttu-id="67a3b-324">SUT は、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-324">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="67a3b-325">アプリのインデックス ページ (*Pages/Index.cshtml* と *Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-325">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="67a3b-326">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-326">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="67a3b-327">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-327">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="67a3b-328">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-328">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="67a3b-329">アプリのデータベース コンテキスト クラスである `AppDbContext` (*Data/AppDbContext.cs*) には、データアクセス層 (DAL) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-329">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="67a3b-330">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-330">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="67a3b-331">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-331">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="67a3b-332">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-332">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="67a3b-333">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-333">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="67a3b-334">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-334">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="67a3b-335">アプリはリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-335">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="67a3b-336">詳細については、[インフラストラクチャの永続化レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)および[コントローラー ロジックのテスト](/aspnet/core/mvc/controllers/testing)に関する記事を参照してください (このサンプルはリポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="67a3b-336">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="67a3b-337">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="67a3b-337">Test app organization</span></span>

<span data-ttu-id="67a3b-338">テスト アプリは、*tests/RazorPagesProject.Tests* ディレクトリにあるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-338">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="67a3b-339">テスト アプリのディレクトリ</span><span class="sxs-lookup"><span data-stu-id="67a3b-339">Test app directory</span></span> | <span data-ttu-id="67a3b-340">説明</span><span class="sxs-lookup"><span data-stu-id="67a3b-340">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="67a3b-341">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="67a3b-341">*AuthTests*</span></span> | <span data-ttu-id="67a3b-342">次のテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-342">Contains test methods for:</span></span><ul><li><span data-ttu-id="67a3b-343">認証されていないユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="67a3b-343">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="67a3b-344">モック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> で認証されたユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="67a3b-344">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="67a3b-345">GitHub ユーザー プロファイルを取得し、プロファイルのユーザー ログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="67a3b-345">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="67a3b-346">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="67a3b-346">*BasicTests*</span></span> | <span data-ttu-id="67a3b-347">ルーティングおよびコンテンツ タイプのテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-347">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="67a3b-348">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="67a3b-348">*IntegrationTests*</span></span> | <span data-ttu-id="67a3b-349">カスタム `WebApplicationFactory` クラスを使用したインデックス ページの統合テストを含みます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-349">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="67a3b-350">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="67a3b-350">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="67a3b-351">*Utilities.cs* には、データベースにテスト データをシードする `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-351">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="67a3b-352">*HtmlHelpers.cs* には、テスト メソッドで使用する AngleSharp `IHtmlDocument` を返すメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-352">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="67a3b-353">*HttpClientExtensions.cs* は、SUT に要求を送信する `SendAsync` のオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-353">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="67a3b-354">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-354">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="67a3b-355">統合テストは、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を含む [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-355">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="67a3b-356">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージはテスト ホストとテスト サーバーを構成するために使用されるため、テスト アプリのプロジェクト ファイルまたはテスト アプリの開発者構成で直接 `TestHost` および `TestServer` パッケージを参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-356">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="67a3b-357">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="67a3b-357">**Seeding the database for testing**</span></span>

<span data-ttu-id="67a3b-358">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-358">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="67a3b-359">たとえば、削除テストでは、データベース レコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも 1 つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-359">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="67a3b-360">このサンプル アプリでは、*Utilities.cs* で 3 つのメッセージを使用してデータベースをシードします。このメッセージは、テストを実行する際に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-360">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="67a3b-361">SUT のデータベース コンテキストは、`Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-361">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="67a3b-362">テスト アプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行された*後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-362">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="67a3b-363">テストに異なるデータベースを使用するには、アプリのデータベース コンテキストを `builder.ConfigureServices`で置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-363">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="67a3b-364">詳細については、「[WebApplicationFactory のカスタマイズ](#customize-webapplicationfactory)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-364">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="67a3b-365">まだ [Web ホスト](xref:fundamentals/host/web-host)を使用している SUT の場合、テスト アプリの `builder.ConfigureServices` コールバックは、SUT の `Startup.ConfigureServices` コードの "*前に*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-365">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="67a3b-366">テスト アプリの `builder.ConfigureTestServices` コールバックは、"*後で*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-366">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="67a3b-367">統合テストでは、データベース、ファイル システム、ネットワークなど、アプリのサポート インフラストラクチャを含むレベルで、アプリのコンポーネントが正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-367">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="67a3b-368">ASP.NET Core では、単体テスト フレームワークとテスト Web ホストおよびメモリ内テスト サーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-368">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="67a3b-369">このトピックを読むには、単体テストの基本的な知識があることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-369">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="67a3b-370">テストの概念を理解していない場合は、「[.NET Core の単体テストと .NET Standard」](/dotnet/core/testing/) のトピックとリンクされたコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-370">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="67a3b-371">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="67a3b-371">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="67a3b-372">このサンプル アプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-372">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="67a3b-373">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-373">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="67a3b-374">[Razor Pages の概要](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="67a3b-374">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="67a3b-375">[Razor Pages の使用を開始する](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="67a3b-375">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="67a3b-376">[Razor Pages の単体テスト](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="67a3b-376">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="67a3b-377">SPA をテストする場合は、ブラウザーを自動化できる [Selenium](https://www.seleniumhq.org/) などのツールを推奨します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-377">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="67a3b-378">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="67a3b-378">Introduction to integration tests</span></span>

<span data-ttu-id="67a3b-379">統合テストでは [単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-379">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="67a3b-380">単体テストは、個々のクラス メソッドなど、分離されたソフトウェア コンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-380">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="67a3b-381">統合テストでは、2 つ以上のアプリ コンポーネントが、連携して予想される結果を生成することを確認します。これは、要求を完全に処理するために必要なすべてのコンポーネントを含む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-381">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="67a3b-382">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-382">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="67a3b-383">データベース</span><span class="sxs-lookup"><span data-stu-id="67a3b-383">Database</span></span>
* <span data-ttu-id="67a3b-384">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="67a3b-384">File system</span></span>
* <span data-ttu-id="67a3b-385">ネットワーク アプライアンス</span><span class="sxs-lookup"><span data-stu-id="67a3b-385">Network appliances</span></span>
* <span data-ttu-id="67a3b-386">要求 - 応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="67a3b-386">Request-response pipeline</span></span>

<span data-ttu-id="67a3b-387">単体テストでは、インフラストラクチャ コンポーネントの代わりに、*フェイク*または*モック オブジェクト*と呼ばれる、作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-387">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="67a3b-388">単体テストと比較すると、統合テストは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-388">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="67a3b-389">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-389">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="67a3b-390">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-390">Require more code and data processing.</span></span>
* <span data-ttu-id="67a3b-391">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-391">Take longer to run.</span></span>

<span data-ttu-id="67a3b-392">そのため、統合テストの使用は最も重要なインフラストラクチャ シナリオに限定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-392">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="67a3b-393">単体テストと統合テストのどちらを使用しても動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-393">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="67a3b-394">統合テストは、データベースとファイル システムを使用するデータおよびファイル アクセスのすべての順列として記述してはいけません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-394">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="67a3b-395">通常は、アプリ全体でデータベースやファイル システムを操作する場所がいくつあったとしても、的を絞った一連の読み取り、書き込み、更新、削除の統合テストを行うことで、データベースおよびファイル システム コンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-395">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="67a3b-396">これらのコンポーネントと連携するメソッドのロジックのルーチン テストには、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-396">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="67a3b-397">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-397">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="67a3b-398">統合テストの説明では、テスト対象のプロジェクトをよく*テスト対象システム*、または短縮して "SUT" と呼びます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-398">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="67a3b-399">*このトピック全体で、テスト対象の ASP.NET Core アプリを指すために "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="67a3b-399">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="67a3b-400">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="67a3b-400">ASP.NET Core integration tests</span></span>

<span data-ttu-id="67a3b-401">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-401">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="67a3b-402">テスト プロジェクトは、テストを格納して実行するために使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-402">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="67a3b-403">テスト プロジェクトは SUT への参照を含みます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-403">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="67a3b-404">テスト プロジェクトは、SUT のテスト Web ホストを作成し、テスト サーバー クライアントを使用して SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-404">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="67a3b-405">テスト ランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-405">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="67a3b-406">統合テストでは、通常の *Arrange (配置)* 、*Act (実行)* 、および *Assert (確認)* のテスト ステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="67a3b-406">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="67a3b-407">SUT の Web ホストが構成されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-407">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="67a3b-408">アプリに要求を送信するためのテスト サーバー クライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-408">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="67a3b-409">*Arrange (配置)* テスト ステップが実行されます。テスト アプリが要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-409">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="67a3b-410">*Act (実行)* テスト ステップが実行されます。クライアントは要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-410">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="67a3b-411">*Assert (確認)* テスト ステップが実行されます。*実際*の応答は、*予測される*応答に基づき、*成功*または*失敗*として検証されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-411">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="67a3b-412">このプロセスは、すべてのテストが実行されるまで続行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-412">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="67a3b-413">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-413">The test results are reported.</span></span>

<span data-ttu-id="67a3b-414">通常、テスト Web ホストは、アプリの通常のテスト用の Web ホストとは異なる方法で構成されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-414">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="67a3b-415">たとえば、テスト用に別のデータベースまたは異なるアプリ設定を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-415">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="67a3b-416">テスト Web ホストやメモリ内テスト サーバー ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャ コンポーネントは、[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-416">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="67a3b-417">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-417">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="67a3b-418">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-418">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="67a3b-419">依存関係ファイル ( *.deps*) を SUT からテスト プロジェクトの *bin* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-419">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="67a3b-420">テストを実行したときに、静的なファイルとページ/ビューが検出されるように、[コンテンツ ルート](xref:fundamentals/index#content-root)を SUT のプロジェクト ルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-420">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="67a3b-421">[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) クラスを提供し、`TestServer` を使用して SUT のブートストラップを効率化します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-421">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="67a3b-422">[単体テストの](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)ドキュメントでは、テスト プロジェクトとテスト ランナーを設定する方法、テストを実行する方法の詳細な手順、テストおよびテスト クラスの命名方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-422">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="67a3b-423">アプリのテスト プロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-423">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="67a3b-424">これにより、インフラストラクチャ テスト コンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-424">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="67a3b-425">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-425">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="67a3b-426">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いがありません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-426">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="67a3b-427">唯一の違いは、テストの命名方法です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-427">The only difference is in how the tests are named.</span></span> <span data-ttu-id="67a3b-428">Razor Pages アプリでは、ページ エンドポイントのテストは通常、ページ モデル クラスにちなんだ名前が付けられます (たとえば、`IndexPageTests` では Index ページのコンポーネントの統合テストが行われます)。</span><span class="sxs-lookup"><span data-stu-id="67a3b-428">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="67a3b-429">MVC アプリでは、テストは通常、コントローラー クラス別に編成され、テストするコントローラーにちなんだ名前が付けられます (たとえば、`HomeControllerTests` は Home コントローラーのコンポーネントの統合テストを行います)。</span><span class="sxs-lookup"><span data-stu-id="67a3b-429">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="67a3b-430">テスト アプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="67a3b-430">Test app prerequisites</span></span>

<span data-ttu-id="67a3b-431">テスト プロジェクトは、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-431">The test project must:</span></span>

* <span data-ttu-id="67a3b-432">次のパッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-432">Reference the following packages:</span></span>
  * [<span data-ttu-id="67a3b-433">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="67a3b-433">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="67a3b-434">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="67a3b-434">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="67a3b-435">プロジェクト ファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-435">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="67a3b-436">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照する場合は、Web SDK が必要です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-436">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="67a3b-437">これらの前提条件は、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で確認できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-437">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="67a3b-438">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* ファイルを確認してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-438">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="67a3b-439">このサンプル アプリでは、[xUnit](https://xunit.github.io/) テスト フレームワークと [AngleSharp](https://anglesharp.github.io/) パーサー ライブラリを使用するので、サンプル アプリは以下も参照します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-439">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="67a3b-440">xunit</span><span class="sxs-lookup"><span data-stu-id="67a3b-440">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="67a3b-441">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="67a3b-441">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="67a3b-442">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="67a3b-442">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="67a3b-443">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="67a3b-443">SUT environment</span></span>

<span data-ttu-id="67a3b-444">SUT の [環境](xref:fundamentals/environments) が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-444">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="67a3b-445">既定の WebApplicationFactory を使用した基本的なテスト</span><span class="sxs-lookup"><span data-stu-id="67a3b-445">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="67a3b-446">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) は、統合テスト用の [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-446">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="67a3b-447">`TEntryPoint` は SUT のエントリ ポイント クラスであり、通常は `Startup` クラスです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-447">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="67a3b-448">テスト クラスは、クラスにテストが含まれていることを示すために*クラス フィクスチャ* インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内テストの共有オブジェクト インスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-448">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="67a3b-449">次のテスト クラス `BasicTests` は、`WebApplicationFactory` を使用して SUT をブートストラップし、テスト メソッド `Get_EndpointsReturnSuccessAndCorrectContentType` に [HttpClient](/dotnet/api/system.net.http.httpclient) を提供します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-449">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="67a3b-450">このメソッドは、複数のアプリ ページで応答状態コードが成功かどうか (200-299 の範囲の状態コード) と、`Content-Type` ヘッダーが `text/html; charset=utf-8` であるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-450">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="67a3b-451">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) は `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトに従い、Cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-451">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="67a3b-452">既定では、[GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、要求間で必須でない Cookie は保持されません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-452">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="67a3b-453">TempData プロバイダーで使用されているような必須ではない Cookie を保持するには、テストに必須であることをマークします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-453">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="67a3b-454">Cookie を必須としてマークする手順については、[必須 Cookie](xref:security/gdpr#essential-cookies) に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-454">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="67a3b-455">WebApplicationFactory のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="67a3b-455">Customize WebApplicationFactory</span></span>

<span data-ttu-id="67a3b-456">Web ホストの構成は、`WebApplicationFactory` から継承して 1 つ以上のカスタム ファクトリを作成することで、テスト クラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-456">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="67a3b-457">`WebApplicationFactory` から継承し、[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost) をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-457">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="67a3b-458">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) では、[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) を使用してサービス コレクションを構成できます。これはアプリの `Startup.ConfigureServices` の前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-458">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="67a3b-459">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) では、[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) を使用して、サービス コレクション内の登録済みサービスをオーバーライドしたり変更したりできます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-459">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="67a3b-460">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-460">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="67a3b-461">このメソッドについては、[統合テストのサンプル: テスト アプリの構成](#test-app-organization)に関するセクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-461">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="67a3b-462">テスト クラスでカスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-462">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="67a3b-463">次の例では、`IndexPageTests` クラスでファクトリを使用しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-463">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="67a3b-464">サンプル アプリのクライアントは、`HttpClient` がリダイレクトに従わないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-464">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="67a3b-465">[モック認証](#mock-authentication)のセクションで後述するように、これによってアプリの最初の応答結果を確認するテストが可能になります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-465">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="67a3b-466">これらのテストでは、多くの場合、最初の応答は `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-466">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="67a3b-467">一般的なテストでは、`HttpClient` およびヘルパー メソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-467">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="67a3b-468">SUT に対する POST 要求は、アプリの[偽造防止データ保護システム](xref:security/data-protection/introduction)によって自動的に行われる偽造防止チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-468">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="67a3b-469">テストで POST 要求を実行するには、テスト アプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-469">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="67a3b-470">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="67a3b-470">Make a request for the page.</span></span>
1. <span data-ttu-id="67a3b-471">応答の偽造防止 Cookie と要求検証トークンを解析します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-471">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="67a3b-472">偽造防止 Cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="67a3b-472">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="67a3b-473">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパー メソッド (*Helpers/HtmlHelpers.cs*) は、次のメソッドで [AngleSharp](https://anglesharp.github.io/) パーサーを使用して偽造防止チェック処理を行います。</span><span class="sxs-lookup"><span data-stu-id="67a3b-473">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="67a3b-474">`GetDocumentAsync`:[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-474">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="67a3b-475">`GetDocumentAsync` は、元の `HttpResponseMessage` に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-475">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="67a3b-476">詳しくは、[AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-476">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="67a3b-477">`HttpClient` の `SendAsync` 拡張メソッドは、[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) を作成し、[SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-477">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="67a3b-478">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-478">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="67a3b-479">フォームの送信ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="67a3b-479">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="67a3b-480">フォームの値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="67a3b-480">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="67a3b-481">送信ボタン (`IHtmlElement`) とフォームの値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="67a3b-481">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="67a3b-482">[AngleSharp](https://anglesharp.github.io/) は、このトピックとサンプル アプリのデモンストレーションのために使用するサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-482">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="67a3b-483">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-483">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="67a3b-484">[Html Agility Pack (HAP)](https://html-agility-pack.net/) などの他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-484">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="67a3b-485">もう 1 つの方法として、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造防止 Cookie を直接処理する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-485">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="67a3b-486">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="67a3b-486">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="67a3b-487">テスト メソッド内で追加の構成が必要な場合は、[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) が構成によってさらにカスタマイズされた [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) を使用して新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-487">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="67a3b-488">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テスト メソッドは、`WithWebHostBuilder` の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-488">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="67a3b-489">このテストでは、SUT からフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-489">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="67a3b-490">`IndexPageTests` クラス内の別のテストは、データベース内のすべてのレコードを削除する操作を実行します。この操作が `Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行される可能性があるため、このテスト メソッド内でデータベースの再シード処理を行い、確実に SUT が削除するレコードが存在するようにしています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-490">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="67a3b-491">SUT に対する要求では、SUT の `messages` フォームの最初の [削除] ボタンの選択がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-491">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="67a3b-492">クライアントのオプション</span><span class="sxs-lookup"><span data-stu-id="67a3b-492">Client options</span></span>

<span data-ttu-id="67a3b-493">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-493">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="67a3b-494">オプション</span><span class="sxs-lookup"><span data-stu-id="67a3b-494">Option</span></span> | <span data-ttu-id="67a3b-495">説明</span><span class="sxs-lookup"><span data-stu-id="67a3b-495">Description</span></span> | <span data-ttu-id="67a3b-496">Default</span><span class="sxs-lookup"><span data-stu-id="67a3b-496">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="67a3b-497">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="67a3b-497">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="67a3b-498">`HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-498">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="67a3b-499">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="67a3b-499">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="67a3b-500">`HttpClient` インスタンスのベース アドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-500">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="67a3b-501">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="67a3b-501">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="67a3b-502">`HttpClient` インスタンスが Cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-502">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="67a3b-503">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="67a3b-503">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="67a3b-504">`HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-504">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="67a3b-505">7</span><span class="sxs-lookup"><span data-stu-id="67a3b-505">7</span></span> |

<span data-ttu-id="67a3b-506">`WebApplicationFactoryClientOptions` クラスを作成し、それを [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="67a3b-506">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="67a3b-507">モック サービスの注入</span><span class="sxs-lookup"><span data-stu-id="67a3b-507">Inject mock services</span></span>

<span data-ttu-id="67a3b-508">ホスト ビルダーで [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) を呼び出すと、テストでサービスをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-508">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="67a3b-509">**モック サービスを注入するには、SUT に `Startup` クラスが存在し、そこに `Startup.ConfigureServices` メソッドが存在している必要があります。**</span><span class="sxs-lookup"><span data-stu-id="67a3b-509">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="67a3b-510">サンプルの SUT には、引用符を返すスコープ サービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-510">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="67a3b-511">インデックス ページが要求されると、インデックス ページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-511">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="67a3b-512">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-512">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="67a3b-513">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-513">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="67a3b-514">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-514">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="67a3b-515">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-515">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="67a3b-516">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-516">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="67a3b-517">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-517">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="67a3b-518">統合テストでサービスと引用符の注入をテストするため、テストは SUT にモック サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-518">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="67a3b-519">モック サービスは、アプリの `QuoteService` をテスト アプリが提供する `TestQuoteService` と呼ばれるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-519">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="67a3b-520">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="67a3b-520">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="67a3b-521">`ConfigureTestServices` が呼び出され、スコープ サービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-521">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="67a3b-522">`TestQuoteService` によって指定された引用符テキストがテストの実行中に生成されたマークアップに反映されるため、アサーションは成功します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-522">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="67a3b-523">モック認証</span><span class="sxs-lookup"><span data-stu-id="67a3b-523">Mock authentication</span></span>

<span data-ttu-id="67a3b-524">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-524">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="67a3b-525">認証されていないユーザーは、アプリのログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-525">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="67a3b-526">認証されたユーザーには、コンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-526">Returns content for an authenticated user.</span></span>

<span data-ttu-id="67a3b-527">SUT の `/SecurePage` ページは、[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 規約を使用してページに [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) を適用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-527">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="67a3b-528">詳細については、[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-528">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="67a3b-529">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) を `false` に設定することで、[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) がリダイレクトを許可しないように設定しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-529">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="67a3b-530">クライアントがリダイレクトに従うことを許可しないことで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-530">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="67a3b-531">ログイン ページにリダイレクトした後の最終的な状態コード (これは [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode) になります) ではなく、予期される [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) の結果を SUT が返すステータス コードと突き合わせて確認できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-531">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="67a3b-532">最終的なログイン ページ応答 (ここでは、`Location` ヘッダーは存在しません) ではなく、応答ヘッダーの `Location` ヘッダー値が `http://localhost/Identity/Account/Login` で始まることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-532">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="67a3b-533">テスト アプリでは、認証と承認の側面をテストするために [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-533">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="67a3b-534">最小のシナリオでは、[AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-534">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="67a3b-535">`TestAuthHandler` は、認証スキームが `Test` に設定されている場合 (この場合、`AddAuthentication` が `ConfigureTestServices` に登録されています) に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-535">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="67a3b-536">`WebApplicationFactoryClientOptions` の詳細については、「[クライアントのオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-536">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="67a3b-537">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="67a3b-537">Set the environment</span></span>

<span data-ttu-id="67a3b-538">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-538">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="67a3b-539">SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="67a3b-539">To override the SUT's environment:</span></span>

* <span data-ttu-id="67a3b-540">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing` などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-540">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="67a3b-541">テスト アプリで `CreateWebHostBuilder` をオーバーライドして、`ASPNETCORE_ENVIRONMENT` 環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-541">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

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

<span data-ttu-id="67a3b-542">環境は、カスタムの <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> のホスト ビルダーで直接設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-542">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

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

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="67a3b-543">テスト インフラストラクチャがアプリ コンテンツのルート パスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="67a3b-543">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="67a3b-544">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリの `System.Reflection.Assembly.FullName` と同じキーを持つ統合テストを含むアセンブリで [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) を検索することによって、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パスを推測します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-544">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="67a3b-545">正しいキーを持つ属性が見つからない場合、`WebApplicationFactory` はフォールバックしてソリューション ファイル ( *.sln*) を検索し、`TEntryPoint` アセンブリ名をソリューション ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-545">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="67a3b-546">アプリのルート ディレクトリ (コンテンツ ルート パス) は、ビューやコンテンツのファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-546">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="67a3b-547">シャドウ コピーの無効化</span><span class="sxs-lookup"><span data-stu-id="67a3b-547">Disable shadow copying</span></span>

<span data-ttu-id="67a3b-548">シャドウ コピーを行うと、テストが出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-548">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="67a3b-549">テストが適切に機能するように、シャドウ コピーを無効化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="67a3b-549">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="67a3b-550">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では、xUnit を使用して正しい構成設定の *xunit.runner.json* ファイルを含めることにより、xUnit のシャドウ コピーを無効化しています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-550">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="67a3b-551">詳しくは、[JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-551">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="67a3b-552">次の内容を使用して、テスト プロジェクトのルートに *xunit.runner.json* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-552">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="67a3b-553">Visual Studio を使用する場合は、ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピーする]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-553">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="67a3b-554">Visual Studio を使用しない場合は、テスト アプリのプロジェクト ファイルに `Content` ターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-554">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="67a3b-555">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="67a3b-555">Disposal of objects</span></span>

<span data-ttu-id="67a3b-556">`IClassFixture` 実装のテストを実行した後、xUnit が [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) を破棄すると、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) と [HttpClient](/dotnet/api/system.net.http.httpclient) が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-556">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="67a3b-557">開発者がインスタンス化したオブジェクトを破棄する必要がある場合は、`IClassFixture` の実装で破棄します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-557">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="67a3b-558">詳細については、「[Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="67a3b-558">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="67a3b-559">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="67a3b-559">Integration tests sample</span></span>

<span data-ttu-id="67a3b-560">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-560">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="67a3b-561">アプリ</span><span class="sxs-lookup"><span data-stu-id="67a3b-561">App</span></span> | <span data-ttu-id="67a3b-562">プロジェクト ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="67a3b-562">Project directory</span></span> | <span data-ttu-id="67a3b-563">説明</span><span class="sxs-lookup"><span data-stu-id="67a3b-563">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="67a3b-564">メッセージ アプリ (SUT)</span><span class="sxs-lookup"><span data-stu-id="67a3b-564">Message app (the SUT)</span></span> | <span data-ttu-id="67a3b-565">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="67a3b-565">*src/RazorPagesProject*</span></span> | <span data-ttu-id="67a3b-566">ユーザーは、メッセージの追加、1 つ削除、すべて削除、および分析を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-566">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="67a3b-567">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="67a3b-567">Test app</span></span> | <span data-ttu-id="67a3b-568">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="67a3b-568">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="67a3b-569">SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-569">Used to integration test the SUT.</span></span> |

<span data-ttu-id="67a3b-570">テストは、[Visual Studio](https://visualstudio.microsoft.com) などの IDE に組み込まれているテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-570">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="67a3b-571">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用している場合は、コマンド プロンプトで *tests/RazorPagesProject.Tests* ディレクトリを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-571">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="67a3b-572">メッセージ アプリ (SUT) の構成</span><span class="sxs-lookup"><span data-stu-id="67a3b-572">Message app (SUT) organization</span></span>

<span data-ttu-id="67a3b-573">SUT は、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-573">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="67a3b-574">アプリのインデックス ページ (*Pages/Index.cshtml* と *Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-574">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="67a3b-575">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-575">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="67a3b-576">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-576">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="67a3b-577">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-577">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="67a3b-578">アプリのデータベース コンテキスト クラスである `AppDbContext` (*Data/AppDbContext.cs*) には、データアクセス層 (DAL) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-578">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="67a3b-579">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-579">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="67a3b-580">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-580">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="67a3b-581">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-581">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="67a3b-582">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-582">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="67a3b-583">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-583">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="67a3b-584">アプリはリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-584">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="67a3b-585">詳細については、[インフラストラクチャの永続化レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)および[コントローラー ロジックのテスト](/aspnet/core/mvc/controllers/testing)に関する記事を参照してください (このサンプルはリポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="67a3b-585">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="67a3b-586">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="67a3b-586">Test app organization</span></span>

<span data-ttu-id="67a3b-587">テスト アプリは、*tests/RazorPagesProject.Tests* ディレクトリにあるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="67a3b-587">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="67a3b-588">テスト アプリのディレクトリ</span><span class="sxs-lookup"><span data-stu-id="67a3b-588">Test app directory</span></span> | <span data-ttu-id="67a3b-589">説明</span><span class="sxs-lookup"><span data-stu-id="67a3b-589">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="67a3b-590">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="67a3b-590">*AuthTests*</span></span> | <span data-ttu-id="67a3b-591">次のテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-591">Contains test methods for:</span></span><ul><li><span data-ttu-id="67a3b-592">認証されていないユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="67a3b-592">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="67a3b-593">モック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> で認証されたユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="67a3b-593">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="67a3b-594">GitHub ユーザー プロファイルを取得し、プロファイルのユーザー ログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="67a3b-594">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="67a3b-595">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="67a3b-595">*BasicTests*</span></span> | <span data-ttu-id="67a3b-596">ルーティングおよびコンテンツ タイプのテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-596">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="67a3b-597">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="67a3b-597">*IntegrationTests*</span></span> | <span data-ttu-id="67a3b-598">カスタム `WebApplicationFactory` クラスを使用したインデックス ページの統合テストを含みます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-598">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="67a3b-599">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="67a3b-599">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="67a3b-600">*Utilities.cs* には、データベースにテスト データをシードする `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-600">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="67a3b-601">*HtmlHelpers.cs* には、テスト メソッドで使用する AngleSharp `IHtmlDocument` を返すメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="67a3b-601">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="67a3b-602">*HttpClientExtensions.cs* は、SUT に要求を送信する `SendAsync` のオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="67a3b-602">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="67a3b-603">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-603">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="67a3b-604">統合テストは、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を含む [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-604">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="67a3b-605">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージはテスト ホストとテスト サーバーを構成するために使用されるため、テスト アプリのプロジェクト ファイルまたはテスト アプリの開発者構成で直接 `TestHost` および `TestServer` パッケージを参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="67a3b-605">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="67a3b-606">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="67a3b-606">**Seeding the database for testing**</span></span>

<span data-ttu-id="67a3b-607">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-607">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="67a3b-608">たとえば、削除テストでは、データベース レコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも 1 つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="67a3b-608">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="67a3b-609">このサンプル アプリでは、*Utilities.cs* で 3 つのメッセージを使用してデータベースをシードします。このメッセージは、テストを実行する際に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="67a3b-609">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="67a3b-610">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="67a3b-610">Additional resources</span></span>

* [<span data-ttu-id="67a3b-611">単体テスト</span><span class="sxs-lookup"><span data-stu-id="67a3b-611">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
