---
<span data-ttu-id="9a3f6-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-102">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-103">'Identity'</span></span>
- <span data-ttu-id="9a3f6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-105">'Razor'</span></span>
- <span data-ttu-id="9a3f6-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="9a3f6-107">ASP.NET Core での統合テスト</span><span class="sxs-lookup"><span data-stu-id="9a3f6-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="9a3f6-108">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Steve Smith](https://ardalis.com/)、[Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9a3f6-109">統合テストでは、データベース、ファイル システム、ネットワークなど、アプリのサポート インフラストラクチャを含むレベルで、アプリのコンポーネントが正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="9a3f6-110">ASP.NET Core では、単体テスト フレームワークとテスト Web ホストおよびメモリ内テスト サーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="9a3f6-111">このトピックを読むには、単体テストの基本的な知識があることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="9a3f6-112">テストの概念を理解していない場合は、「[.NET Core の単体テストと .NET Standard」](/dotnet/core/testing/) のトピックとリンクされたコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="9a3f6-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9a3f6-114">このサンプル アプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="9a3f6-115">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="9a3f6-116">[Razor Pages の概要](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="9a3f6-117">[Razor Pages の使用を開始する](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="9a3f6-118">[Razor Pages の単体テスト](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="9a3f6-119">SPA をテストする場合は、ブラウザーを自動化できる [Selenium](https://www.seleniumhq.org/) などのツールを推奨します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="9a3f6-120">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="9a3f6-120">Introduction to integration tests</span></span>

<span data-ttu-id="9a3f6-121">統合テストでは [単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="9a3f6-122">単体テストは、個々のクラス メソッドなど、分離されたソフトウェア コンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="9a3f6-123">統合テストでは、2 つ以上のアプリ コンポーネントが、連携して予想される結果を生成することを確認します。これは、要求を完全に処理するために必要なすべてのコンポーネントを含む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="9a3f6-124">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="9a3f6-125">データベース</span><span class="sxs-lookup"><span data-stu-id="9a3f6-125">Database</span></span>
* <span data-ttu-id="9a3f6-126">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="9a3f6-126">File system</span></span>
* <span data-ttu-id="9a3f6-127">ネットワーク アプライアンス</span><span class="sxs-lookup"><span data-stu-id="9a3f6-127">Network appliances</span></span>
* <span data-ttu-id="9a3f6-128">要求 - 応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="9a3f6-128">Request-response pipeline</span></span>

<span data-ttu-id="9a3f6-129">単体テストでは、インフラストラクチャ コンポーネントの代わりに、*フェイク*または*モック オブジェクト*と呼ばれる、作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="9a3f6-130">単体テストと比較すると、統合テストは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="9a3f6-131">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="9a3f6-132">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-132">Require more code and data processing.</span></span>
* <span data-ttu-id="9a3f6-133">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-133">Take longer to run.</span></span>

<span data-ttu-id="9a3f6-134">そのため、統合テストの使用は最も重要なインフラストラクチャ シナリオに限定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="9a3f6-135">単体テストと統合テストのどちらを使用しても動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="9a3f6-136">統合テストは、データベースとファイル システムを使用するデータおよびファイル アクセスのすべての順列として記述してはいけません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="9a3f6-137">通常は、アプリ全体でデータベースやファイル システムを操作する場所がいくつあったとしても、的を絞った一連の読み取り、書き込み、更新、削除の統合テストを行うことで、データベースおよびファイル システム コンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="9a3f6-138">これらのコンポーネントと連携するメソッドのロジックのルーチン テストには、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="9a3f6-139">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="9a3f6-140">統合テストの説明では、テスト対象のプロジェクトをよく*テスト対象システム*、または短縮して "SUT" と呼びます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="9a3f6-141">*このトピック全体で、テスト対象の ASP.NET Core アプリを指すために "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="9a3f6-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="9a3f6-142">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="9a3f6-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="9a3f6-143">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="9a3f6-144">テスト プロジェクトは、テストを格納して実行するために使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="9a3f6-145">テスト プロジェクトは SUT への参照を含みます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="9a3f6-146">テスト プロジェクトは、SUT のテスト Web ホストを作成し、テスト サーバー クライアントを使用して SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="9a3f6-147">テスト ランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="9a3f6-148">統合テストでは、通常の *Arrange (配置)* 、*Act (実行)* 、および *Assert (確認)* のテスト ステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="9a3f6-149">SUT の Web ホストが構成されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="9a3f6-150">アプリに要求を送信するためのテスト サーバー クライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="9a3f6-151">*Arrange (配置)* テスト ステップが実行されます。テスト アプリが要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="9a3f6-152">*Act (実行)* テスト ステップが実行されます。クライアントは要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="9a3f6-153">*Assert (確認)* テスト ステップが実行されます。*実際*の応答は、*予測される*応答に基づき、*成功*または*失敗*として検証されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="9a3f6-154">このプロセスは、すべてのテストが実行されるまで続行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="9a3f6-155">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-155">The test results are reported.</span></span>

<span data-ttu-id="9a3f6-156">通常、テスト Web ホストは、アプリの通常のテスト用の Web ホストとは異なる方法で構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="9a3f6-157">たとえば、テスト用に別のデータベースまたは異なるアプリ設定を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="9a3f6-158">テスト Web ホストやメモリ内テスト サーバー ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャ コンポーネントは、[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="9a3f6-159">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="9a3f6-160">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="9a3f6-161">依存関係ファイル ( *.deps*) を SUT からテスト プロジェクトの *bin* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="9a3f6-162">テストを実行したときに、静的なファイルとページ/ビューが検出されるように、[コンテンツ ルート](xref:fundamentals/index#content-root)を SUT のプロジェクト ルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="9a3f6-163">[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) クラスを提供し、`TestServer` を使用して SUT のブートストラップを効率化します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="9a3f6-164">[単体テストの](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)ドキュメントでは、テスト プロジェクトとテスト ランナーを設定する方法、テストを実行する方法の詳細な手順、テストおよびテスト クラスの命名方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="9a3f6-165">アプリのテスト プロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="9a3f6-166">これにより、インフラストラクチャ テスト コンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="9a3f6-167">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="9a3f6-168">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いがありません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="9a3f6-169">唯一の違いは、テストの命名方法です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="9a3f6-170">Razor Pages アプリでは、ページ エンドポイントのテストは通常、ページ モデル クラスにちなんだ名前が付けられます (たとえば、`IndexPageTests` では Index ページのコンポーネントの統合テストが行われます)。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="9a3f6-171">MVC アプリでは、テストは通常、コントローラー クラス別に編成され、テストするコントローラーにちなんだ名前が付けられます (たとえば、`HomeControllerTests` は Home コントローラーのコンポーネントの統合テストを行います)。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="9a3f6-172">テスト アプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="9a3f6-172">Test app prerequisites</span></span>

<span data-ttu-id="9a3f6-173">テスト プロジェクトは、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-173">The test project must:</span></span>

* <span data-ttu-id="9a3f6-174">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージを参照しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="9a3f6-175">プロジェクト ファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="9a3f6-176">これらの前提条件は、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で確認できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="9a3f6-177">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* ファイルを確認してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="9a3f6-178">このサンプル アプリでは、[xUnit](https://xunit.github.io/) テスト フレームワークと [AngleSharp](https://anglesharp.github.io/) パーサー ライブラリを使用するので、サンプル アプリは以下も参照します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="9a3f6-179">xunit</span><span class="sxs-lookup"><span data-stu-id="9a3f6-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="9a3f6-180">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="9a3f6-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="9a3f6-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="9a3f6-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="9a3f6-182">テストでは Entity Framework Core も使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="9a3f6-183">アプリは以下を参照します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-183">The app references:</span></span>

* [<span data-ttu-id="9a3f6-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9a3f6-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="9a3f6-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="9a3f6-186">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9a3f6-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="9a3f6-187">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="9a3f6-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="9a3f6-188">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="9a3f6-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="9a3f6-189">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="9a3f6-189">SUT environment</span></span>

<span data-ttu-id="9a3f6-190">SUT の [環境](xref:fundamentals/environments) が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="9a3f6-191">既定の WebApplicationFactory を使用した基本的なテスト</span><span class="sxs-lookup"><span data-stu-id="9a3f6-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="9a3f6-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) は、統合テスト用の [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="9a3f6-193">`TEntryPoint` は SUT のエントリ ポイント クラスであり、通常は `Startup` クラスです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="9a3f6-194">テスト クラスは、クラスにテストが含まれていることを示すために*クラス フィクスチャ* インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内テストの共有オブジェクト インスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="9a3f6-195">次のテスト クラス `BasicTests` は、`WebApplicationFactory` を使用して SUT をブートストラップし、テスト メソッド `Get_EndpointsReturnSuccessAndCorrectContentType` に [HttpClient](/dotnet/api/system.net.http.httpclient) を提供します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="9a3f6-196">このメソッドは、複数のアプリ ページで応答状態コードが成功かどうか (200-299 の範囲の状態コード) と、`Content-Type` ヘッダーが `text/html; charset=utf-8` であるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="9a3f6-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) は `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトに従い、Cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="9a3f6-198">既定では、[GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、要求間で必須でない Cookie は保持されません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="9a3f6-199">TempData プロバイダーで使用されているような必須ではない Cookie を保持するには、テストに必須であることをマークします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="9a3f6-200">Cookie を必須としてマークする手順については、[必須 Cookie](xref:security/gdpr#essential-cookies) に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="9a3f6-201">WebApplicationFactory のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="9a3f6-202">Web ホストの構成は、`WebApplicationFactory` から継承して 1 つ以上のカスタム ファクトリを作成することで、テスト クラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="9a3f6-203">`WebApplicationFactory` から継承し、[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost) をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="9a3f6-204">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) では、[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) を使用してサービス コレクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="9a3f6-205">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="9a3f6-206">このメソッドについては、[統合テストのサンプル: テスト アプリの構成](#test-app-organization)に関するセクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="9a3f6-207">SUT のデータベース コンテキストは、`Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a3f6-208">テスト アプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行された*後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="9a3f6-209">ASP.NET Core 3.0 リリースの[汎用ホスト](xref:fundamentals/host/generic-host)により、実行順序に関する互換性のない変更が行われています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="9a3f6-210">アプリのデータベースとは異なるデータベースをテストに使用するには、`builder.ConfigureServices` でアプリのデータベース コンテキストを置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="9a3f6-211">まだ [Web ホスト](xref:fundamentals/host/web-host)を使用している SUT の場合、テスト アプリの `builder.ConfigureServices` コールバックは、SUT の `Startup.ConfigureServices` コードの "*前に*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-211">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="9a3f6-212">テスト アプリの `builder.ConfigureTestServices` コールバックは、"*後で*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="9a3f6-213">サンプル アプリでは、データベース コンテキストのサービス記述子を検索し、記述子を使用してサービス登録を削除しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="9a3f6-214">次に、ファクトリは、テストにメモリ内データベースを使用する新しい `ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="9a3f6-215">メモリ内データベースではないデータベースに接続するには、`UseInMemoryDatabase` 呼び出しを変更して、コンテキストを別のデータベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="9a3f6-216">SQL Server テスト データベースを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="9a3f6-217">プロジェクト ファイルで [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="9a3f6-218">データベースへの接続文字列を使用して `UseSqlServer` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="9a3f6-219">テスト クラスでカスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="9a3f6-220">次の例では、`IndexPageTests` クラスでファクトリを使用しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="9a3f6-221">サンプル アプリのクライアントは、`HttpClient` がリダイレクトに従わないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="9a3f6-222">[モック認証](#mock-authentication)のセクションで後述するように、これによってアプリの最初の応答結果を確認するテストが可能になります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="9a3f6-223">これらのテストでは、多くの場合、最初の応答は `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="9a3f6-224">一般的なテストでは、`HttpClient` およびヘルパー メソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="9a3f6-225">SUT に対する POST 要求は、アプリの[偽造防止データ保護システム](xref:security/data-protection/introduction)によって自動的に行われる偽造防止チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="9a3f6-226">テストで POST 要求を実行するには、テスト アプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="9a3f6-227">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-227">Make a request for the page.</span></span>
1. <span data-ttu-id="9a3f6-228">応答の偽造防止 Cookie と要求検証トークンを解析します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="9a3f6-229">偽造防止 Cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="9a3f6-230">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパー メソッド (*Helpers/HtmlHelpers.cs*) は、次のメソッドで [AngleSharp](https://anglesharp.github.io/) パーサーを使用して偽造防止チェック処理を行います。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="9a3f6-231">`GetDocumentAsync`:[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-231">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="9a3f6-232">`GetDocumentAsync` は、元の `HttpResponseMessage` に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="9a3f6-233">詳しくは、[AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="9a3f6-234">`HttpClient` の `SendAsync` 拡張メソッドは、[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) を作成し、[SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="9a3f6-235">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="9a3f6-236">フォームの送信ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="9a3f6-237">フォームの値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="9a3f6-238">送信ボタン (`IHtmlElement`) とフォームの値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="9a3f6-239">[AngleSharp](https://anglesharp.github.io/) は、このトピックとサンプル アプリのデモンストレーションのために使用するサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="9a3f6-240">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="9a3f6-241">[Html Agility Pack (HAP)](https://html-agility-pack.net/) などの他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="9a3f6-242">もう 1 つの方法として、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造防止 Cookie を直接処理する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="9a3f6-243">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="9a3f6-244">テスト メソッド内で追加の構成が必要な場合は、[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) が構成によってさらにカスタマイズされた [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) を使用して新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="9a3f6-245">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テスト メソッドは、`WithWebHostBuilder` の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="9a3f6-246">このテストでは、SUT からフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="9a3f6-247">`IndexPageTests` クラス内の別のテストは、データベース内のすべてのレコードを削除する操作を実行します。この操作が `Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行される可能性があるため、このテスト メソッド内でデータベースの再シード処理を行い、確実に SUT が削除するレコードが存在するようにしています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="9a3f6-248">SUT に対する要求では、SUT の `messages` フォームの最初の [削除] ボタンの選択がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="9a3f6-249">クライアントのオプション</span><span class="sxs-lookup"><span data-stu-id="9a3f6-249">Client options</span></span>

<span data-ttu-id="9a3f6-250">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="9a3f6-251">オプション</span><span class="sxs-lookup"><span data-stu-id="9a3f6-251">Option</span></span> | <span data-ttu-id="9a3f6-252">説明</span><span class="sxs-lookup"><span data-stu-id="9a3f6-252">Description</span></span> | <span data-ttu-id="9a3f6-253">Default</span><span class="sxs-lookup"><span data-stu-id="9a3f6-253">Default</span></span> |
| ---
<span data-ttu-id="9a3f6-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-255">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-256">'Identity'</span></span>
- <span data-ttu-id="9a3f6-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-258">'Razor'</span></span>
- <span data-ttu-id="9a3f6-259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-259">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-261">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-262">'Identity'</span></span>
- <span data-ttu-id="9a3f6-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-264">'Razor'</span></span>
- <span data-ttu-id="9a3f6-265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-267">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-268">'Identity'</span></span>
- <span data-ttu-id="9a3f6-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-270">'Razor'</span></span>
- <span data-ttu-id="9a3f6-271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-273">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-274">'Identity'</span></span>
- <span data-ttu-id="9a3f6-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-276">'Razor'</span></span>
- <span data-ttu-id="9a3f6-277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-277">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-279">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-280">'Identity'</span></span>
- <span data-ttu-id="9a3f6-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-282">'Razor'</span></span>
- <span data-ttu-id="9a3f6-283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-283">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="9a3f6-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | `HttpClient` インスタンスのベース アドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="9a3f6-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | `HttpClient` インスタンスが Cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="9a3f6-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | `HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="9a3f6-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="9a3f6-288">| 7 |</span></span>

<span data-ttu-id="9a3f6-289">`WebApplicationFactoryClientOptions` クラスを作成し、それを [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="9a3f6-290">モック サービスの注入</span><span class="sxs-lookup"><span data-stu-id="9a3f6-290">Inject mock services</span></span>

<span data-ttu-id="9a3f6-291">ホスト ビルダーで [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) を呼び出すと、テストでサービスをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="9a3f6-292">**モック サービスを注入するには、SUT に `Startup` クラスが存在し、そこに `Startup.ConfigureServices` メソッドが存在している必要があります。**</span><span class="sxs-lookup"><span data-stu-id="9a3f6-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="9a3f6-293">サンプルの SUT には、引用符を返すスコープ サービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="9a3f6-294">インデックス ページが要求されると、インデックス ページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="9a3f6-295">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="9a3f6-296">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="9a3f6-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="9a3f6-298">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="9a3f6-299">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="9a3f6-300">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="9a3f6-301">統合テストでサービスと引用符の注入をテストするため、テストは SUT にモック サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="9a3f6-302">モック サービスは、アプリの `QuoteService` をテスト アプリが提供する `TestQuoteService` と呼ばれるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="9a3f6-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="9a3f6-304">`ConfigureTestServices` が呼び出され、スコープ サービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="9a3f6-305">`TestQuoteService` によって指定された引用符テキストがテストの実行中に生成されたマークアップに反映されるため、アサーションは成功します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="9a3f6-306">モック認証</span><span class="sxs-lookup"><span data-stu-id="9a3f6-306">Mock authentication</span></span>

<span data-ttu-id="9a3f6-307">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="9a3f6-308">認証されていないユーザーは、アプリのログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="9a3f6-309">認証されたユーザーには、コンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="9a3f6-310">SUT の `/SecurePage` ページは、[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 規約を使用してページに [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) を適用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="9a3f6-311">詳細については、[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="9a3f6-312">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) を `false` に設定することで、[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) がリダイレクトを許可しないように設定しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="9a3f6-313">クライアントがリダイレクトに従うことを許可しないことで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="9a3f6-314">ログイン ページにリダイレクトした後の最終的な状態コード (これは [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode) になります) ではなく、予期される [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) の結果を SUT が返すステータス コードと突き合わせて確認できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="9a3f6-315">最終的なログイン ページ応答 (ここでは、`Location` ヘッダーは存在しません) ではなく、応答ヘッダーの `Location` ヘッダー値が `http://localhost/Identity/Account/Login` で始まることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="9a3f6-316">テスト アプリでは、認証と承認の側面をテストするために [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="9a3f6-317">最小のシナリオでは、[AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="9a3f6-318">`TestAuthHandler` は、認証スキームが `Test` に設定されている場合 (この場合、`AddAuthentication` が `ConfigureTestServices` に登録されています) に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="9a3f6-319">`WebApplicationFactoryClientOptions` の詳細については、「[クライアントのオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="9a3f6-320">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="9a3f6-320">Set the environment</span></span>

<span data-ttu-id="9a3f6-321">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="9a3f6-322">`IHostBuilder` を使用しているときに SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="9a3f6-323">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing` などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="9a3f6-324">テスト アプリで `CreateHostBuilder` をオーバーライドして、`ASPNETCORE` で始まる環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="9a3f6-325">SUT が Web ホスト (`IWebHostBuilder`) を使用している場合は、`CreateWebHostBuilder` をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="9a3f6-326">テスト インフラストラクチャがアプリ コンテンツのルート パスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="9a3f6-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="9a3f6-327">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリの `System.Reflection.Assembly.FullName` と同じキーを持つ統合テストを含むアセンブリで [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) を検索することによって、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パスを推測します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="9a3f6-328">正しいキーを持つ属性が見つからない場合、`WebApplicationFactory` はフォールバックしてソリューション ファイル ( *.sln*) を検索し、`TEntryPoint` アセンブリ名をソリューション ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="9a3f6-329">アプリのルート ディレクトリ (コンテンツ ルート パス) は、ビューやコンテンツのファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="9a3f6-330">シャドウ コピーの無効化</span><span class="sxs-lookup"><span data-stu-id="9a3f6-330">Disable shadow copying</span></span>

<span data-ttu-id="9a3f6-331">シャドウ コピーを行うと、テストが出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="9a3f6-332">テストが適切に機能するように、シャドウ コピーを無効化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="9a3f6-333">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では、xUnit を使用して正しい構成設定の *xunit.runner.json* ファイルを含めることにより、xUnit のシャドウ コピーを無効化しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="9a3f6-334">詳しくは、[JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="9a3f6-335">次の内容を使用して、テスト プロジェクトのルートに *xunit.runner.json* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="9a3f6-336">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="9a3f6-336">Disposal of objects</span></span>

<span data-ttu-id="9a3f6-337">`IClassFixture` 実装のテストを実行した後、xUnit が [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) を破棄すると、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) と [HttpClient](/dotnet/api/system.net.http.httpclient) が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="9a3f6-338">開発者がインスタンス化したオブジェクトを破棄する必要がある場合は、`IClassFixture` の実装で破棄します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="9a3f6-339">詳細については、「[Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="9a3f6-340">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="9a3f6-340">Integration tests sample</span></span>

<span data-ttu-id="9a3f6-341">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="9a3f6-342">アプリ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-342">App</span></span> | <span data-ttu-id="9a3f6-343">プロジェクト ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-343">Project directory</span></span> | <span data-ttu-id="9a3f6-344">説明</span><span class="sxs-lookup"><span data-stu-id="9a3f6-344">Description</span></span> |
| --- | ---
<span data-ttu-id="9a3f6-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-346">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-347">'Identity'</span></span>
- <span data-ttu-id="9a3f6-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-349">'Razor'</span></span>
- <span data-ttu-id="9a3f6-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-352">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-353">'Identity'</span></span>
- <span data-ttu-id="9a3f6-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-355">'Razor'</span></span>
- <span data-ttu-id="9a3f6-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-358">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-359">'Identity'</span></span>
- <span data-ttu-id="9a3f6-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-361">'Razor'</span></span>
- <span data-ttu-id="9a3f6-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-364">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-365">'Identity'</span></span>
- <span data-ttu-id="9a3f6-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-367">'Razor'</span></span>
- <span data-ttu-id="9a3f6-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-370">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-371">'Identity'</span></span>
- <span data-ttu-id="9a3f6-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-373">'Razor'</span></span>
- <span data-ttu-id="9a3f6-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-376">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-377">'Identity'</span></span>
- <span data-ttu-id="9a3f6-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-379">'Razor'</span></span>
- <span data-ttu-id="9a3f6-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-380">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-382">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-383">'Identity'</span></span>
- <span data-ttu-id="9a3f6-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-385">'Razor'</span></span>
- <span data-ttu-id="9a3f6-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-388">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-389">'Identity'</span></span>
- <span data-ttu-id="9a3f6-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-391">'Razor'</span></span>
- <span data-ttu-id="9a3f6-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-394">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-395">'Identity'</span></span>
- <span data-ttu-id="9a3f6-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-397">'Razor'</span></span>
- <span data-ttu-id="9a3f6-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-398">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | ユーザーは、メッセージの追加、1 つ削除、すべて削除、および分析を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="9a3f6-400">| | Test app | *tests/RazorPagesProject.Tests* | SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="9a3f6-401">テストは、[Visual Studio](https://visualstudio.microsoft.com) などの IDE に組み込まれているテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="9a3f6-402">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用している場合は、コマンド プロンプトで *tests/RazorPagesProject.Tests* ディレクトリを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="9a3f6-403">メッセージ アプリ (SUT) の構成</span><span class="sxs-lookup"><span data-stu-id="9a3f6-403">Message app (SUT) organization</span></span>

<span data-ttu-id="9a3f6-404">SUT は、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="9a3f6-405">アプリのインデックス ページ (*Pages/Index.cshtml* と *Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="9a3f6-406">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="9a3f6-407">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="9a3f6-408">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="9a3f6-409">アプリのデータベース コンテキスト クラスである `AppDbContext` (*Data/AppDbContext.cs*) には、データアクセス層 (DAL) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="9a3f6-410">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="9a3f6-411">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="9a3f6-412">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="9a3f6-413">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="9a3f6-414">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="9a3f6-415">アプリはリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="9a3f6-416">詳細については、[インフラストラクチャの永続化レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)および[コントローラー ロジックのテスト](/aspnet/core/mvc/controllers/testing)に関する記事を参照してください (このサンプルはリポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="9a3f6-417">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="9a3f6-417">Test app organization</span></span>

<span data-ttu-id="9a3f6-418">テスト アプリは、*tests/RazorPagesProject.Tests* ディレクトリにあるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="9a3f6-419">テスト アプリのディレクトリ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-419">Test app directory</span></span> | <span data-ttu-id="9a3f6-420">説明</span><span class="sxs-lookup"><span data-stu-id="9a3f6-420">Description</span></span> |
| ---
<span data-ttu-id="9a3f6-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-422">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-423">'Identity'</span></span>
- <span data-ttu-id="9a3f6-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-425">'Razor'</span></span>
- <span data-ttu-id="9a3f6-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-428">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-429">'Identity'</span></span>
- <span data-ttu-id="9a3f6-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-431">'Razor'</span></span>
- <span data-ttu-id="9a3f6-432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-434">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-435">'Identity'</span></span>
- <span data-ttu-id="9a3f6-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-437">'Razor'</span></span>
- <span data-ttu-id="9a3f6-438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-440">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-441">'Identity'</span></span>
- <span data-ttu-id="9a3f6-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-443">'Razor'</span></span>
- <span data-ttu-id="9a3f6-444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-446">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-447">'Identity'</span></span>
- <span data-ttu-id="9a3f6-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-449">'Razor'</span></span>
- <span data-ttu-id="9a3f6-450">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-452">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-453">'Identity'</span></span>
- <span data-ttu-id="9a3f6-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-455">'Razor'</span></span>
- <span data-ttu-id="9a3f6-456">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-458">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-459">'Identity'</span></span>
- <span data-ttu-id="9a3f6-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-461">'Razor'</span></span>
- <span data-ttu-id="9a3f6-462">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-462">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-464">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-465">'Identity'</span></span>
- <span data-ttu-id="9a3f6-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-467">'Razor'</span></span>
- <span data-ttu-id="9a3f6-468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-470">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-471">'Identity'</span></span>
- <span data-ttu-id="9a3f6-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-473">'Razor'</span></span>
- <span data-ttu-id="9a3f6-474">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-476">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-477">'Identity'</span></span>
- <span data-ttu-id="9a3f6-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-479">'Razor'</span></span>
- <span data-ttu-id="9a3f6-480">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-480">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-481">------ | | *AuthTests* | 次のテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="9a3f6-482">認証されていないユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="9a3f6-483">モック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> で認証されたユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="9a3f6-484">GitHub ユーザー プロファイルを取得し、プロファイルのユーザー ログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="9a3f6-485">| | *BasicTests* | ルーティングおよびコンテンツ タイプのテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="9a3f6-486">| | *IntegrationTests* | カスタム `WebApplicationFactory` クラスを使用したインデックス ページの統合テストを含みます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="9a3f6-487">| | *Helpers/Utilities* | </span><span class="sxs-lookup"><span data-stu-id="9a3f6-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="9a3f6-488">*Utilities.cs* には、データベースにテスト データをシードする `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="9a3f6-489">*HtmlHelpers.cs* には、テスト メソッドで使用する AngleSharp `IHtmlDocument` を返すメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="9a3f6-490">*HttpClientExtensions.cs* は、SUT に要求を送信する `SendAsync` のオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="9a3f6-491">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="9a3f6-492">統合テストは、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を含む [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="9a3f6-493">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージはテスト ホストとテスト サーバーを構成するために使用されるため、テスト アプリのプロジェクト ファイルまたはテスト アプリの開発者構成で直接 `TestHost` および `TestServer` パッケージを参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="9a3f6-494">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="9a3f6-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="9a3f6-495">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="9a3f6-496">たとえば、削除テストでは、データベース レコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも 1 つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="9a3f6-497">このサンプル アプリでは、*Utilities.cs* で 3 つのメッセージを使用してデータベースをシードします。このメッセージは、テストを実行する際に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="9a3f6-498">SUT のデータベース コンテキストは、`Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a3f6-499">テスト アプリの `builder.ConfigureServices` コールバックは、アプリの `Startup.ConfigureServices` コードが実行された*後*に実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="9a3f6-500">テストに異なるデータベースを使用するには、アプリのデータベース コンテキストを `builder.ConfigureServices`で置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="9a3f6-501">詳細については、「[WebApplicationFactory のカスタマイズ](#customize-webapplicationfactory)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="9a3f6-502">まだ [Web ホスト](xref:fundamentals/host/web-host)を使用している SUT の場合、テスト アプリの `builder.ConfigureServices` コールバックは、SUT の `Startup.ConfigureServices` コードの "*前に*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-502">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="9a3f6-503">テスト アプリの `builder.ConfigureTestServices` コールバックは、"*後で*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9a3f6-504">統合テストでは、データベース、ファイル システム、ネットワークなど、アプリのサポート インフラストラクチャを含むレベルで、アプリのコンポーネントが正しく機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="9a3f6-505">ASP.NET Core では、単体テスト フレームワークとテスト Web ホストおよびメモリ内テスト サーバーを使用した統合テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="9a3f6-506">このトピックを読むには、単体テストの基本的な知識があることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="9a3f6-507">テストの概念を理解していない場合は、「[.NET Core の単体テストと .NET Standard」](/dotnet/core/testing/) のトピックとリンクされたコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="9a3f6-508">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9a3f6-509">このサンプル アプリは Razor Pages アプリであり、Razor Pages の基本を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="9a3f6-510">Razor Pages に慣れていない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="9a3f6-511">[Razor Pages の概要](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="9a3f6-512">[Razor Pages の使用を開始する](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="9a3f6-513">[Razor Pages の単体テスト](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="9a3f6-514">SPA をテストする場合は、ブラウザーを自動化できる [Selenium](https://www.seleniumhq.org/) などのツールを推奨します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="9a3f6-515">統合テストの概要</span><span class="sxs-lookup"><span data-stu-id="9a3f6-515">Introduction to integration tests</span></span>

<span data-ttu-id="9a3f6-516">統合テストでは [単体テスト](/dotnet/core/testing/)よりも広範なレベルでアプリのコンポーネントを評価します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="9a3f6-517">単体テストは、個々のクラス メソッドなど、分離されたソフトウェア コンポーネントをテストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="9a3f6-518">統合テストでは、2 つ以上のアプリ コンポーネントが、連携して予想される結果を生成することを確認します。これは、要求を完全に処理するために必要なすべてのコンポーネントを含む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="9a3f6-519">これらの広範なテストは、アプリのインフラストラクチャとフレームワーク全体をテストするために使用されます。多くの場合、次のコンポーネントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="9a3f6-520">データベース</span><span class="sxs-lookup"><span data-stu-id="9a3f6-520">Database</span></span>
* <span data-ttu-id="9a3f6-521">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="9a3f6-521">File system</span></span>
* <span data-ttu-id="9a3f6-522">ネットワーク アプライアンス</span><span class="sxs-lookup"><span data-stu-id="9a3f6-522">Network appliances</span></span>
* <span data-ttu-id="9a3f6-523">要求 - 応答パイプライン</span><span class="sxs-lookup"><span data-stu-id="9a3f6-523">Request-response pipeline</span></span>

<span data-ttu-id="9a3f6-524">単体テストでは、インフラストラクチャ コンポーネントの代わりに、*フェイク*または*モック オブジェクト*と呼ばれる、作成済みのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="9a3f6-525">単体テストと比較すると、統合テストは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="9a3f6-526">アプリが運用環境で使用する実際のコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="9a3f6-527">より多くのコードとデータ処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-527">Require more code and data processing.</span></span>
* <span data-ttu-id="9a3f6-528">実行に時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-528">Take longer to run.</span></span>

<span data-ttu-id="9a3f6-529">そのため、統合テストの使用は最も重要なインフラストラクチャ シナリオに限定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="9a3f6-530">単体テストと統合テストのどちらを使用しても動作をテストできる場合は、単体テストを選択します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="9a3f6-531">統合テストは、データベースとファイル システムを使用するデータおよびファイル アクセスのすべての順列として記述してはいけません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="9a3f6-532">通常は、アプリ全体でデータベースやファイル システムを操作する場所がいくつあったとしても、的を絞った一連の読み取り、書き込み、更新、削除の統合テストを行うことで、データベースおよびファイル システム コンポーネントを適切にテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="9a3f6-533">これらのコンポーネントと連携するメソッドのロジックのルーチン テストには、単体テストを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="9a3f6-534">単体テストでは、インフラストラクチャのフェイク/モックを使用することにより、テストの実行時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="9a3f6-535">統合テストの説明では、テスト対象のプロジェクトをよく*テスト対象システム*、または短縮して "SUT" と呼びます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="9a3f6-536">*このトピック全体で、テスト対象の ASP.NET Core アプリを指すために "SUT" を使用します。*</span><span class="sxs-lookup"><span data-stu-id="9a3f6-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="9a3f6-537">ASP.NET Core 統合テスト</span><span class="sxs-lookup"><span data-stu-id="9a3f6-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="9a3f6-538">ASP.NET Core の統合テストには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="9a3f6-539">テスト プロジェクトは、テストを格納して実行するために使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="9a3f6-540">テスト プロジェクトは SUT への参照を含みます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="9a3f6-541">テスト プロジェクトは、SUT のテスト Web ホストを作成し、テスト サーバー クライアントを使用して SUT との要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="9a3f6-542">テスト ランナーは、テストを実行し、テスト結果を報告するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="9a3f6-543">統合テストでは、通常の *Arrange (配置)* 、*Act (実行)* 、および *Assert (確認)* のテスト ステップを含む一連のイベントに従います。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="9a3f6-544">SUT の Web ホストが構成されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="9a3f6-545">アプリに要求を送信するためのテスト サーバー クライアントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="9a3f6-546">*Arrange (配置)* テスト ステップが実行されます。テスト アプリが要求を準備します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="9a3f6-547">*Act (実行)* テスト ステップが実行されます。クライアントは要求を送信し、応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="9a3f6-548">*Assert (確認)* テスト ステップが実行されます。*実際*の応答は、*予測される*応答に基づき、*成功*または*失敗*として検証されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="9a3f6-549">このプロセスは、すべてのテストが実行されるまで続行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="9a3f6-550">テスト結果が報告されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-550">The test results are reported.</span></span>

<span data-ttu-id="9a3f6-551">通常、テスト Web ホストは、アプリの通常のテスト用の Web ホストとは異なる方法で構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="9a3f6-552">たとえば、テスト用に別のデータベースまたは異なるアプリ設定を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="9a3f6-553">テスト Web ホストやメモリ内テスト サーバー ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) などのインフラストラクチャ コンポーネントは、[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージによって提供または管理されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="9a3f6-554">このパッケージを使用すると、テストの作成と実行を効率化できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="9a3f6-555">`Microsoft.AspNetCore.Mvc.Testing` パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="9a3f6-556">依存関係ファイル ( *.deps*) を SUT からテスト プロジェクトの *bin* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="9a3f6-557">テストを実行したときに、静的なファイルとページ/ビューが検出されるように、[コンテンツ ルート](xref:fundamentals/index#content-root)を SUT のプロジェクト ルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="9a3f6-558">[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) クラスを提供し、`TestServer` を使用して SUT のブートストラップを効率化します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="9a3f6-559">[単体テストの](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)ドキュメントでは、テスト プロジェクトとテスト ランナーを設定する方法、テストを実行する方法の詳細な手順、テストおよびテスト クラスの命名方法に関する推奨事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="9a3f6-560">アプリのテスト プロジェクトを作成する場合は、単体テストを統合テストから別のプロジェクトに分離します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="9a3f6-561">これにより、インフラストラクチャ テスト コンポーネントが誤って単体テストに含まれないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="9a3f6-562">単体テストと統合テストを分離すると、実行されるテストのセットを制御することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="9a3f6-563">Razor Pages アプリと MVC アプリのテストの構成には、ほぼ違いがありません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="9a3f6-564">唯一の違いは、テストの命名方法です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="9a3f6-565">Razor Pages アプリでは、ページ エンドポイントのテストは通常、ページ モデル クラスにちなんだ名前が付けられます (たとえば、`IndexPageTests` では Index ページのコンポーネントの統合テストが行われます)。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="9a3f6-566">MVC アプリでは、テストは通常、コントローラー クラス別に編成され、テストするコントローラーにちなんだ名前が付けられます (たとえば、`HomeControllerTests` は Home コントローラーのコンポーネントの統合テストを行います)。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="9a3f6-567">テスト アプリの前提条件</span><span class="sxs-lookup"><span data-stu-id="9a3f6-567">Test app prerequisites</span></span>

<span data-ttu-id="9a3f6-568">テスト プロジェクトは、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-568">The test project must:</span></span>

* <span data-ttu-id="9a3f6-569">次のパッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="9a3f6-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="9a3f6-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="9a3f6-571">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="9a3f6-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="9a3f6-572">プロジェクト ファイル (`<Project Sdk="Microsoft.NET.Sdk.Web">`) で Web SDK を指定しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="9a3f6-573">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照する場合は、Web SDK が必要です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9a3f6-574">これらの前提条件は、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)で確認できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="9a3f6-575">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* ファイルを確認してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="9a3f6-576">このサンプル アプリでは、[xUnit](https://xunit.github.io/) テスト フレームワークと [AngleSharp](https://anglesharp.github.io/) パーサー ライブラリを使用するので、サンプル アプリは以下も参照します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="9a3f6-577">xunit</span><span class="sxs-lookup"><span data-stu-id="9a3f6-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="9a3f6-578">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="9a3f6-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="9a3f6-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="9a3f6-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="9a3f6-580">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="9a3f6-580">SUT environment</span></span>

<span data-ttu-id="9a3f6-581">SUT の [環境](xref:fundamentals/environments) が設定されていない場合、環境は既定で開発になります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="9a3f6-582">既定の WebApplicationFactory を使用した基本的なテスト</span><span class="sxs-lookup"><span data-stu-id="9a3f6-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="9a3f6-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) は、統合テスト用の [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="9a3f6-584">`TEntryPoint` は SUT のエントリ ポイント クラスであり、通常は `Startup` クラスです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="9a3f6-585">テスト クラスは、クラスにテストが含まれていることを示すために*クラス フィクスチャ* インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を実装し、クラス内テストの共有オブジェクト インスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="9a3f6-586">次のテスト クラス `BasicTests` は、`WebApplicationFactory` を使用して SUT をブートストラップし、テスト メソッド `Get_EndpointsReturnSuccessAndCorrectContentType` に [HttpClient](/dotnet/api/system.net.http.httpclient) を提供します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="9a3f6-587">このメソッドは、複数のアプリ ページで応答状態コードが成功かどうか (200-299 の範囲の状態コード) と、`Content-Type` ヘッダーが `text/html; charset=utf-8` であるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="9a3f6-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) は `HttpClient` のインスタンスを作成します。このインスタンスは、自動的にリダイレクトに従い、Cookie を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="9a3f6-589">既定では、[GDPR 同意ポリシー](xref:security/gdpr)が有効になっている場合、要求間で必須でない Cookie は保持されません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="9a3f6-590">TempData プロバイダーで使用されているような必須ではない Cookie を保持するには、テストに必須であることをマークします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="9a3f6-591">Cookie を必須としてマークする手順については、[必須 Cookie](xref:security/gdpr#essential-cookies) に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="9a3f6-592">WebApplicationFactory のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="9a3f6-593">Web ホストの構成は、`WebApplicationFactory` から継承して 1 つ以上のカスタム ファクトリを作成することで、テスト クラスとは別に作成できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="9a3f6-594">`WebApplicationFactory` から継承し、[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost) をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="9a3f6-595">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) では、[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) を使用してサービス コレクションを構成できます。これはアプリの `Startup.ConfigureServices` の前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9a3f6-596">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) では、[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) を使用して、サービス コレクション内の登録済みサービスをオーバーライドしたり変更したりできます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="9a3f6-597">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)でのデータベースのシード処理は、`InitializeDbForTests` メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="9a3f6-598">このメソッドについては、[統合テストのサンプル: テスト アプリの構成](#test-app-organization)に関するセクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="9a3f6-599">テスト クラスでカスタム `CustomWebApplicationFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="9a3f6-600">次の例では、`IndexPageTests` クラスでファクトリを使用しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="9a3f6-601">サンプル アプリのクライアントは、`HttpClient` がリダイレクトに従わないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="9a3f6-602">[モック認証](#mock-authentication)のセクションで後述するように、これによってアプリの最初の応答結果を確認するテストが可能になります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="9a3f6-603">これらのテストでは、多くの場合、最初の応答は `Location` ヘッダーを持つリダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="9a3f6-604">一般的なテストでは、`HttpClient` およびヘルパー メソッドを使用して、要求と応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="9a3f6-605">SUT に対する POST 要求は、アプリの[偽造防止データ保護システム](xref:security/data-protection/introduction)によって自動的に行われる偽造防止チェックを満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="9a3f6-606">テストで POST 要求を実行するには、テスト アプリで次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="9a3f6-607">ページに対して要求を行います。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-607">Make a request for the page.</span></span>
1. <span data-ttu-id="9a3f6-608">応答の偽造防止 Cookie と要求検証トークンを解析します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="9a3f6-609">偽造防止 Cookie と要求検証トークンを使用して POST 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="9a3f6-610">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)の `SendAsync` ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) と `GetDocumentAsync` ヘルパー メソッド (*Helpers/HtmlHelpers.cs*) は、次のメソッドで [AngleSharp](https://anglesharp.github.io/) パーサーを使用して偽造防止チェック処理を行います。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="9a3f6-611">`GetDocumentAsync`:[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) を受け取り、`IHtmlDocument` を返します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-611">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="9a3f6-612">`GetDocumentAsync` は、元の `HttpResponseMessage` に基づいて*仮想応答*を準備するファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="9a3f6-613">詳しくは、[AngleSharp のドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="9a3f6-614">`HttpClient` の `SendAsync` 拡張メソッドは、[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) を作成し、[SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) を呼び出して、SUT に要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="9a3f6-615">`SendAsync` のオーバーロードは、HTML フォーム (`IHtmlFormElement`) と次のものを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="9a3f6-616">フォームの送信ボタン (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="9a3f6-617">フォームの値コレクション (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="9a3f6-618">送信ボタン (`IHtmlElement`) とフォームの値 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="9a3f6-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="9a3f6-619">[AngleSharp](https://anglesharp.github.io/) は、このトピックとサンプル アプリのデモンストレーションのために使用するサードパーティ製の解析ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="9a3f6-620">ASP.NET Core アプリの統合テストでは、AngleSharp はサポートされていないか、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="9a3f6-621">[Html Agility Pack (HAP)](https://html-agility-pack.net/) などの他のパーサーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="9a3f6-622">もう 1 つの方法として、偽造防止システムの要求検証トークンを処理するコードを記述し、偽造防止 Cookie を直接処理する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="9a3f6-623">WithWebHostBuilder を使用したクライアントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="9a3f6-624">テスト メソッド内で追加の構成が必要な場合は、[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) が構成によってさらにカスタマイズされた [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) を使用して新しい `WebApplicationFactory` を作成します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="9a3f6-625">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) の `Post_DeleteMessageHandler_ReturnsRedirectToRoot` テスト メソッドは、`WithWebHostBuilder` の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="9a3f6-626">このテストでは、SUT からフォーム送信をトリガーすることによって、データベース内のレコードの削除を実行します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="9a3f6-627">`IndexPageTests` クラス内の別のテストは、データベース内のすべてのレコードを削除する操作を実行します。この操作が `Post_DeleteMessageHandler_ReturnsRedirectToRoot` メソッドの前に実行される可能性があるため、このテスト メソッド内でデータベースの再シード処理を行い、確実に SUT が削除するレコードが存在するようにしています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="9a3f6-628">SUT に対する要求では、SUT の `messages` フォームの最初の [削除] ボタンの選択がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="9a3f6-629">クライアントのオプション</span><span class="sxs-lookup"><span data-stu-id="9a3f6-629">Client options</span></span>

<span data-ttu-id="9a3f6-630">次の表に、`HttpClient` インスタンスを作成するときに使用できる既定の [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) を示します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="9a3f6-631">オプション</span><span class="sxs-lookup"><span data-stu-id="9a3f6-631">Option</span></span> | <span data-ttu-id="9a3f6-632">説明</span><span class="sxs-lookup"><span data-stu-id="9a3f6-632">Description</span></span> | <span data-ttu-id="9a3f6-633">Default</span><span class="sxs-lookup"><span data-stu-id="9a3f6-633">Default</span></span> |
| ---
<span data-ttu-id="9a3f6-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-635">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-636">'Identity'</span></span>
- <span data-ttu-id="9a3f6-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-638">'Razor'</span></span>
- <span data-ttu-id="9a3f6-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-639">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-641">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-642">'Identity'</span></span>
- <span data-ttu-id="9a3f6-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-644">'Razor'</span></span>
- <span data-ttu-id="9a3f6-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-647">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-648">'Identity'</span></span>
- <span data-ttu-id="9a3f6-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-650">'Razor'</span></span>
- <span data-ttu-id="9a3f6-651">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-653">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-654">'Identity'</span></span>
- <span data-ttu-id="9a3f6-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-656">'Razor'</span></span>
- <span data-ttu-id="9a3f6-657">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-657">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-659">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-660">'Identity'</span></span>
- <span data-ttu-id="9a3f6-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-662">'Razor'</span></span>
- <span data-ttu-id="9a3f6-663">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-663">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient` インスタンスがリダイレクト応答に自動的に従うかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="9a3f6-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | `HttpClient` インスタンスのベース アドレスを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="9a3f6-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | `HttpClient` インスタンスが Cookie を処理する必要があるかどうかを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="9a3f6-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | `HttpClient` インスタンスが従う必要があるリダイレクト応答の最大数を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="9a3f6-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="9a3f6-668">| 7 |</span></span>

<span data-ttu-id="9a3f6-669">`WebApplicationFactoryClientOptions` クラスを作成し、それを [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) メソッドに渡します (既定値については、コード例を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="9a3f6-670">モック サービスの注入</span><span class="sxs-lookup"><span data-stu-id="9a3f6-670">Inject mock services</span></span>

<span data-ttu-id="9a3f6-671">ホスト ビルダーで [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) を呼び出すと、テストでサービスをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="9a3f6-672">**モック サービスを注入するには、SUT に `Startup` クラスが存在し、そこに `Startup.ConfigureServices` メソッドが存在している必要があります。**</span><span class="sxs-lookup"><span data-stu-id="9a3f6-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="9a3f6-673">サンプルの SUT には、引用符を返すスコープ サービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="9a3f6-674">インデックス ページが要求されると、インデックス ページの非表示フィールドに引用符が埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="9a3f6-675">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="9a3f6-676">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="9a3f6-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="9a3f6-678">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="9a3f6-679">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="9a3f6-680">次のマークアップは、SUT アプリの実行時に生成されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="9a3f6-681">統合テストでサービスと引用符の注入をテストするため、テストは SUT にモック サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="9a3f6-682">モック サービスは、アプリの `QuoteService` をテスト アプリが提供する `TestQuoteService` と呼ばれるサービスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="9a3f6-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="9a3f6-684">`ConfigureTestServices` が呼び出され、スコープ サービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="9a3f6-685">`TestQuoteService` によって指定された引用符テキストがテストの実行中に生成されたマークアップに反映されるため、アサーションは成功します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="9a3f6-686">モック認証</span><span class="sxs-lookup"><span data-stu-id="9a3f6-686">Mock authentication</span></span>

<span data-ttu-id="9a3f6-687">`AuthTests` クラスのテストは、セキュリティで保護されたエンドポイントであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="9a3f6-688">認証されていないユーザーは、アプリのログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="9a3f6-689">認証されたユーザーには、コンテンツを返します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="9a3f6-690">SUT の `/SecurePage` ページは、[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 規約を使用してページに [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) を適用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="9a3f6-691">詳細については、[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="9a3f6-692">`Get_SecurePageRedirectsAnUnauthenticatedUser` テストでは、[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) を `false` に設定することで、[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) がリダイレクトを許可しないように設定しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="9a3f6-693">クライアントがリダイレクトに従うことを許可しないことで、次のチェックを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="9a3f6-694">ログイン ページにリダイレクトした後の最終的な状態コード (これは [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode) になります) ではなく、予期される [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) の結果を SUT が返すステータス コードと突き合わせて確認できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="9a3f6-695">最終的なログイン ページ応答 (ここでは、`Location` ヘッダーは存在しません) ではなく、応答ヘッダーの `Location` ヘッダー値が `http://localhost/Identity/Account/Login` で始まることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="9a3f6-696">テスト アプリでは、認証と承認の側面をテストするために [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) の <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> をモックすることができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="9a3f6-697">最小のシナリオでは、[AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="9a3f6-698">`TestAuthHandler` は、認証スキームが `Test` に設定されている場合 (この場合、`AddAuthentication` が `ConfigureTestServices` に登録されています) に、ユーザーを認証するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="9a3f6-699">`WebApplicationFactoryClientOptions` の詳細については、「[クライアントのオプション](#client-options)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="9a3f6-700">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="9a3f6-700">Set the environment</span></span>

<span data-ttu-id="9a3f6-701">既定では、SUT のホストとアプリ環境は、開発環境を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="9a3f6-702">SUT の環境をオーバーライドするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="9a3f6-703">`ASPNETCORE_ENVIRONMENT` 環境変数 (たとえば、`Staging`、`Production`、または `Testing` などのカスタム値) を設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="9a3f6-704">テスト アプリで `CreateWebHostBuilder` をオーバーライドして、`ASPNETCORE_ENVIRONMENT` 環境変数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

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

<span data-ttu-id="9a3f6-705">環境は、カスタムの <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> のホスト ビルダーで直接設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

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

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="9a3f6-706">テスト インフラストラクチャがアプリ コンテンツのルート パスを推測する方法</span><span class="sxs-lookup"><span data-stu-id="9a3f6-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="9a3f6-707">`WebApplicationFactory` コンストラクターは、`TEntryPoint` アセンブリの `System.Reflection.Assembly.FullName` と同じキーを持つ統合テストを含むアセンブリで [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) を検索することによって、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パスを推測します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="9a3f6-708">正しいキーを持つ属性が見つからない場合、`WebApplicationFactory` はフォールバックしてソリューション ファイル ( *.sln*) を検索し、`TEntryPoint` アセンブリ名をソリューション ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="9a3f6-709">アプリのルート ディレクトリ (コンテンツ ルート パス) は、ビューやコンテンツのファイルを検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="9a3f6-710">シャドウ コピーの無効化</span><span class="sxs-lookup"><span data-stu-id="9a3f6-710">Disable shadow copying</span></span>

<span data-ttu-id="9a3f6-711">シャドウ コピーを行うと、テストが出力ディレクトリとは異なるディレクトリで実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="9a3f6-712">テストが適切に機能するように、シャドウ コピーを無効化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="9a3f6-713">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)では、xUnit を使用して正しい構成設定の *xunit.runner.json* ファイルを含めることにより、xUnit のシャドウ コピーを無効化しています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="9a3f6-714">詳しくは、[JSON を使用した xUnit の構成](https://xunit.github.io/docs/configuring-with-json.html)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="9a3f6-715">次の内容を使用して、テスト プロジェクトのルートに *xunit.runner.json* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="9a3f6-716">Visual Studio を使用する場合は、ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピーする]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="9a3f6-717">Visual Studio を使用しない場合は、テスト アプリのプロジェクト ファイルに `Content` ターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="9a3f6-718">オブジェクトの破棄</span><span class="sxs-lookup"><span data-stu-id="9a3f6-718">Disposal of objects</span></span>

<span data-ttu-id="9a3f6-719">`IClassFixture` 実装のテストを実行した後、xUnit が [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) を破棄すると、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) と [HttpClient](/dotnet/api/system.net.http.httpclient) が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="9a3f6-720">開発者がインスタンス化したオブジェクトを破棄する必要がある場合は、`IClassFixture` の実装で破棄します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="9a3f6-721">詳細については、「[Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="9a3f6-722">統合テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="9a3f6-722">Integration tests sample</span></span>

<span data-ttu-id="9a3f6-723">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="9a3f6-724">アプリ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-724">App</span></span> | <span data-ttu-id="9a3f6-725">プロジェクト ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-725">Project directory</span></span> | <span data-ttu-id="9a3f6-726">説明</span><span class="sxs-lookup"><span data-stu-id="9a3f6-726">Description</span></span> |
| --- | ---
<span data-ttu-id="9a3f6-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-728">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-729">'Identity'</span></span>
- <span data-ttu-id="9a3f6-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-731">'Razor'</span></span>
- <span data-ttu-id="9a3f6-732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-734">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-735">'Identity'</span></span>
- <span data-ttu-id="9a3f6-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-737">'Razor'</span></span>
- <span data-ttu-id="9a3f6-738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-740">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-741">'Identity'</span></span>
- <span data-ttu-id="9a3f6-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-743">'Razor'</span></span>
- <span data-ttu-id="9a3f6-744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-746">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-747">'Identity'</span></span>
- <span data-ttu-id="9a3f6-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-749">'Razor'</span></span>
- <span data-ttu-id="9a3f6-750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-752">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-753">'Identity'</span></span>
- <span data-ttu-id="9a3f6-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-755">'Razor'</span></span>
- <span data-ttu-id="9a3f6-756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-758">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-759">'Identity'</span></span>
- <span data-ttu-id="9a3f6-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-761">'Razor'</span></span>
- <span data-ttu-id="9a3f6-762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-762">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-764">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-765">'Identity'</span></span>
- <span data-ttu-id="9a3f6-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-767">'Razor'</span></span>
- <span data-ttu-id="9a3f6-768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-770">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-771">'Identity'</span></span>
- <span data-ttu-id="9a3f6-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-773">'Razor'</span></span>
- <span data-ttu-id="9a3f6-774">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-776">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-777">'Identity'</span></span>
- <span data-ttu-id="9a3f6-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-779">'Razor'</span></span>
- <span data-ttu-id="9a3f6-780">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-780">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | ユーザーは、メッセージの追加、1 つ削除、すべて削除、および分析を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="9a3f6-782">| | Test app | *tests/RazorPagesProject.Tests* | SUT の統合テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="9a3f6-783">テストは、[Visual Studio](https://visualstudio.microsoft.com) などの IDE に組み込まれているテスト機能を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="9a3f6-784">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用している場合は、コマンド プロンプトで *tests/RazorPagesProject.Tests* ディレクトリを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="9a3f6-785">メッセージ アプリ (SUT) の構成</span><span class="sxs-lookup"><span data-stu-id="9a3f6-785">Message app (SUT) organization</span></span>

<span data-ttu-id="9a3f6-786">SUT は、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="9a3f6-787">アプリのインデックス ページ (*Pages/Index.cshtml* と *Pages/Index.cshtml.cs*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="9a3f6-788">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="9a3f6-789">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="9a3f6-790">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="9a3f6-791">アプリのデータベース コンテキスト クラスである `AppDbContext` (*Data/AppDbContext.cs*) には、データアクセス層 (DAL) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="9a3f6-792">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="9a3f6-793">アプリには、認証されたユーザーのみがアクセスできる `/SecurePage` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="9a3f6-794">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="9a3f6-795">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="9a3f6-796">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="9a3f6-797">アプリはリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="9a3f6-798">詳細については、[インフラストラクチャの永続化レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)および[コントローラー ロジックのテスト](/aspnet/core/mvc/controllers/testing)に関する記事を参照してください (このサンプルはリポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="9a3f6-799">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="9a3f6-799">Test app organization</span></span>

<span data-ttu-id="9a3f6-800">テスト アプリは、*tests/RazorPagesProject.Tests* ディレクトリにあるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="9a3f6-801">テスト アプリのディレクトリ</span><span class="sxs-lookup"><span data-stu-id="9a3f6-801">Test app directory</span></span> | <span data-ttu-id="9a3f6-802">説明</span><span class="sxs-lookup"><span data-stu-id="9a3f6-802">Description</span></span> |
| ---
<span data-ttu-id="9a3f6-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-804">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-805">'Identity'</span></span>
- <span data-ttu-id="9a3f6-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-807">'Razor'</span></span>
- <span data-ttu-id="9a3f6-808">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-810">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-811">'Identity'</span></span>
- <span data-ttu-id="9a3f6-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-813">'Razor'</span></span>
- <span data-ttu-id="9a3f6-814">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-816">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-817">'Identity'</span></span>
- <span data-ttu-id="9a3f6-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-819">'Razor'</span></span>
- <span data-ttu-id="9a3f6-820">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-822">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-823">'Identity'</span></span>
- <span data-ttu-id="9a3f6-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-825">'Razor'</span></span>
- <span data-ttu-id="9a3f6-826">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-828">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-829">'Identity'</span></span>
- <span data-ttu-id="9a3f6-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-831">'Razor'</span></span>
- <span data-ttu-id="9a3f6-832">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-834">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-835">'Identity'</span></span>
- <span data-ttu-id="9a3f6-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-837">'Razor'</span></span>
- <span data-ttu-id="9a3f6-838">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-840">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-841">'Identity'</span></span>
- <span data-ttu-id="9a3f6-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-843">'Razor'</span></span>
- <span data-ttu-id="9a3f6-844">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-844">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-846">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-847">'Identity'</span></span>
- <span data-ttu-id="9a3f6-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-849">'Razor'</span></span>
- <span data-ttu-id="9a3f6-850">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-852">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-853">'Identity'</span></span>
- <span data-ttu-id="9a3f6-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-855">'Razor'</span></span>
- <span data-ttu-id="9a3f6-856">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9a3f6-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9a3f6-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-858">'Blazor'</span></span>
- <span data-ttu-id="9a3f6-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-859">'Identity'</span></span>
- <span data-ttu-id="9a3f6-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="9a3f6-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9a3f6-861">'Razor'</span></span>
- <span data-ttu-id="9a3f6-862">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9a3f6-862">'SignalR' uid:</span></span> 

<span data-ttu-id="9a3f6-863">------ | | *AuthTests* | 次のテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="9a3f6-864">認証されていないユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="9a3f6-865">モック <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> で認証されたユーザーがセキュリティで保護されたページにアクセスする。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="9a3f6-866">GitHub ユーザー プロファイルを取得し、プロファイルのユーザー ログインを確認する。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="9a3f6-867">| | *BasicTests* | ルーティングおよびコンテンツ タイプのテスト メソッドを含みます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="9a3f6-868">| | *IntegrationTests* | カスタム `WebApplicationFactory` クラスを使用したインデックス ページの統合テストを含みます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="9a3f6-869">| | *Helpers/Utilities* | </span><span class="sxs-lookup"><span data-stu-id="9a3f6-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="9a3f6-870">*Utilities.cs* には、データベースにテスト データをシードする `InitializeDbForTests` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="9a3f6-871">*HtmlHelpers.cs* には、テスト メソッドで使用する AngleSharp `IHtmlDocument` を返すメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="9a3f6-872">*HttpClientExtensions.cs* は、SUT に要求を送信する `SendAsync` のオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="9a3f6-873">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="9a3f6-874">統合テストは、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を含む [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="9a3f6-875">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) パッケージはテスト ホストとテスト サーバーを構成するために使用されるため、テスト アプリのプロジェクト ファイルまたはテスト アプリの開発者構成で直接 `TestHost` および `TestServer` パッケージを参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="9a3f6-876">**テスト用のデータベースのシード処理**</span><span class="sxs-lookup"><span data-stu-id="9a3f6-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="9a3f6-877">統合テストでは、通常、テストを実行する前に、データベース内に小さなデータセットが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="9a3f6-878">たとえば、削除テストでは、データベース レコードの削除を呼び出します。そのため、削除要求を成功させるには、データベースに少なくとも 1 つのレコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="9a3f6-879">このサンプル アプリでは、*Utilities.cs* で 3 つのメッセージを使用してデータベースをシードします。このメッセージは、テストを実行する際に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="9a3f6-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9a3f6-880">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9a3f6-880">Additional resources</span></span>

* [<span data-ttu-id="9a3f6-881">単体テスト</span><span class="sxs-lookup"><span data-stu-id="9a3f6-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
