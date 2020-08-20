---
title: ASP.NET Core のコントローラーのロジックをテストする
author: ardalis
description: Moq と xUnit を使って ASP.NET Core のコントローラーのロジックをテストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: mvc/controllers/testing
ms.openlocfilehash: d6c70d828d6c2f62f9e7b849a299df3077f2da32
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635230"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a><span data-ttu-id="8e103-103">ASP.NET Core でコントローラーのロジックの単体テストを行う</span><span class="sxs-lookup"><span data-stu-id="8e103-103">Unit test controller logic in ASP.NET Core</span></span>

<span data-ttu-id="8e103-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8e103-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e103-105">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)では、アプリの一部をインフラストラクチャや依存関係から切り離してテストすることが必要とされます。</span><span class="sxs-lookup"><span data-stu-id="8e103-105">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="8e103-106">コントローラー ロジックの単体テストを行うと、単一のアクションの内容のみがテストされ、その依存関係やフレームワーク自体の動作はテストされません。</span><span class="sxs-lookup"><span data-stu-id="8e103-106">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

## <a name="unit-testing-controllers"></a><span data-ttu-id="8e103-107">コントローラーの単体テスト</span><span class="sxs-lookup"><span data-stu-id="8e103-107">Unit testing controllers</span></span>

<span data-ttu-id="8e103-108">コントローラーのアクションの単体テストは、コントローラーの動作に注目するように設定します。</span><span class="sxs-lookup"><span data-stu-id="8e103-108">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="8e103-109">コントローラーの単体テストでは、[フィルター](xref:mvc/controllers/filters)、[ルーティング](xref:fundamentals/routing)、[モデル バインド](xref:mvc/models/model-binding)などのシナリオは除外します。</span><span class="sxs-lookup"><span data-stu-id="8e103-109">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="8e103-110">まとまって要求に応答するコンポーネント間のインタラクションをカバーするテストは、*統合テスト*によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-110">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="8e103-111">統合テストの詳細については、「<xref:test/integration-tests>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e103-111">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="8e103-112">カスタム フィルターやルートを作成している場合は、コントローラーの特定のアクションに対するテストの一部としてではなく、単体テストを切り離して実行します。</span><span class="sxs-lookup"><span data-stu-id="8e103-112">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="8e103-113">コントローラーの単体テストを理解するために、次のサンプル アプリ内のコントローラーを確認してください。</span><span class="sxs-lookup"><span data-stu-id="8e103-113">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> 

<span data-ttu-id="8e103-114">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8e103-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8e103-115">Home コントローラーは、ブレーンストーミング セッションの一覧を表示し、POST 要求で新しいブレーンストーミング セッションを作成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="8e103-115">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="8e103-116">上記のコントローラーの説明:</span><span class="sxs-lookup"><span data-stu-id="8e103-116">The preceding controller:</span></span>

* <span data-ttu-id="8e103-117">[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に従います。</span><span class="sxs-lookup"><span data-stu-id="8e103-117">Follows the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="8e103-118">[依存関係の注入 (DI)](xref:fundamentals/dependency-injection) を予期して、`IBrainstormSessionRepository` のインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="8e103-118">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="8e103-119">モック オブジェクト フレームワークを使用するモックされた `IBrainstormSessionRepository` サービスでテストできます ([Moq](https://www.nuget.org/packages/Moq/) サービスなど)。</span><span class="sxs-lookup"><span data-stu-id="8e103-119">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="8e103-120">*モック オブジェクト*は、テストで使用されるプロパティとメソッドの動作が事前定義されている加工オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="8e103-120">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="8e103-121">詳細については、「[Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests)」(統合テストの概要) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e103-121">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="8e103-122">`HTTP GET Index` メソッドにはループや分岐はなく、1 つのメソッドを呼び出すだけです。</span><span class="sxs-lookup"><span data-stu-id="8e103-122">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="8e103-123">このアクションに対する単体テストでは、以下を行います。</span><span class="sxs-lookup"><span data-stu-id="8e103-123">The unit test for this action:</span></span>

* <span data-ttu-id="8e103-124">`IBrainstormSessionRepository` メソッドを使用する `GetTestSessions` サービスをモックする。</span><span class="sxs-lookup"><span data-stu-id="8e103-124">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="8e103-125">`GetTestSessions` が日付とセッション名を持つ 2 つのモック ブレーンストーミング セッションを作成する。</span><span class="sxs-lookup"><span data-stu-id="8e103-125">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="8e103-126">`Index` メソッドを実行する。</span><span class="sxs-lookup"><span data-stu-id="8e103-126">Executes the `Index` method.</span></span>
* <span data-ttu-id="8e103-127">メソッドによって返された結果に関するアサーションを行う。</span><span class="sxs-lookup"><span data-stu-id="8e103-127">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="8e103-128"><xref:Microsoft.AspNetCore.Mvc.ViewResult> が返される。</span><span class="sxs-lookup"><span data-stu-id="8e103-128">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="8e103-129">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) は `StormSessionViewModel`。</span><span class="sxs-lookup"><span data-stu-id="8e103-129">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="8e103-130">`ViewDataDictionary.Model`に格納された 2 つのブレーンストーミング セッションがある。</span><span class="sxs-lookup"><span data-stu-id="8e103-130">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="8e103-131">Home コントローラーの `HTTP POST Index` メソッドのテストでは、以下が検証されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-131">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="8e103-132">[ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) が `false` の場合、アクション メソッドは、*400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> と適切なデータを返す。</span><span class="sxs-lookup"><span data-stu-id="8e103-132">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="8e103-133">`ModelState.IsValid` が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="8e103-133">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="8e103-134">リポジトリの `Add` メソッドが呼び出される。</span><span class="sxs-lookup"><span data-stu-id="8e103-134">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="8e103-135"><xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> と適切な引数が返される。</span><span class="sxs-lookup"><span data-stu-id="8e103-135">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="8e103-136">下の最初のテストに示すように、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> を使用してエラーを追加することで、モデルの無効状態をテストできます。</span><span class="sxs-lookup"><span data-stu-id="8e103-136">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="8e103-137">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が有効でない場合は、GET 要求と同じ `ViewResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-137">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="8e103-138">このテストでは、無効なモデルを渡すことは試していません。</span><span class="sxs-lookup"><span data-stu-id="8e103-138">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="8e103-139">モデル バインドが実行されていないため、無効なモデルを渡すことは効果的なアプローチではありません (ただし、[統合テスト](xref:test/integration-tests)ではモデル バインドが使用されます)。</span><span class="sxs-lookup"><span data-stu-id="8e103-139">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="8e103-140">ここでは、モデル バインドはテストされていません。</span><span class="sxs-lookup"><span data-stu-id="8e103-140">In this case, model binding isn't tested.</span></span> <span data-ttu-id="8e103-141">これらの単体テストでは、アクション メソッドのコードだけをテストしています。</span><span class="sxs-lookup"><span data-stu-id="8e103-141">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="8e103-142">2 番目のテストでは、`ModelState` が有効な場合の検証を行います。</span><span class="sxs-lookup"><span data-stu-id="8e103-142">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="8e103-143">新しい `BrainstormSession` が追加される (リポジトリ経由)。</span><span class="sxs-lookup"><span data-stu-id="8e103-143">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="8e103-144">メソッドが `RedirectToActionResult` と予期されるプロパティを返す。</span><span class="sxs-lookup"><span data-stu-id="8e103-144">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="8e103-145">呼び出されないモックの呼び出しは通常は無視されますが、Setup 呼び出しの最後で `Verifiable` を呼び出すと、テスト内でのモック検証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-145">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="8e103-146">これは `mockRepo.Verify` の呼び出しで実行され、予期されるメソッドが呼び出されないとテストは失敗します。</span><span class="sxs-lookup"><span data-stu-id="8e103-146">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="8e103-147">このサンプルで使われている Moq ライブラリにより、検証可能な ("厳密な") モックと検証不可能なモック ("厳密でない" モックまたはスタブとも呼ばれます) を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="8e103-147">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="8e103-148">詳しくは、Moq の「[Customizing Mock Behavior](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)」(モックの動作のカスタマイズ) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8e103-148">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="8e103-149">サンプル アプリの [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) は、特定のブレーンストーミング セッションに関する情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="8e103-149">[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="8e103-150">このコントローラーには、無効な `id` 値を処理するロジックが含まれています (これらのシナリオをカバーするために、次のサンプルには 2 つの `return` シナリオがあります)。</span><span class="sxs-lookup"><span data-stu-id="8e103-150">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="8e103-151">最終的な `return` ステートメントにより、新しい `StormSessionViewModel` がビュー (*Controllers/SessionController.cs*) に返されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-151">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="8e103-152">Session コントローラーの `Index`アクション内に各 `return` シナリオ用の 1 つのテストを含む単体テスト:</span><span class="sxs-lookup"><span data-stu-id="8e103-152">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="8e103-153">Ideas コントローラーに移ると、アプリは、機能を Web API として `api/ideas` ルートで公開します。</span><span class="sxs-lookup"><span data-stu-id="8e103-153">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="8e103-154">ブレーンストーミング セッションに関連付けられたアイデアの一覧 (`IdeaDTO`) が、`ForSession` メソッドによって返される。</span><span class="sxs-lookup"><span data-stu-id="8e103-154">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="8e103-155">`Create` メソッドが新しいアイデアをセッションに追加する。</span><span class="sxs-lookup"><span data-stu-id="8e103-155">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="8e103-156">API の呼び出しでビジネス ドメイン エンティティを直接返すことは避けてください。</span><span class="sxs-lookup"><span data-stu-id="8e103-156">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="8e103-157">ドメイン エンティティ:</span><span class="sxs-lookup"><span data-stu-id="8e103-157">Domain entities:</span></span>

* <span data-ttu-id="8e103-158">多くの場合、クライアントが必要とするもの以上のデータを含みます。</span><span class="sxs-lookup"><span data-stu-id="8e103-158">Often include more data than the client requires.</span></span>
* <span data-ttu-id="8e103-159">アプリの内部ドメイン モデルと一般公開されている API が不必要に結合します。</span><span class="sxs-lookup"><span data-stu-id="8e103-159">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="8e103-160">ドメイン エンティティとクライアントに返される型の間のマッピングは、以下のように実行できます。</span><span class="sxs-lookup"><span data-stu-id="8e103-160">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="8e103-161">サンプル アプリで使用しているように、LINQ `Select` を使用して手動で実行します。</span><span class="sxs-lookup"><span data-stu-id="8e103-161">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="8e103-162">詳細については、「[LINQ (統合言語クエリ)](/dotnet/standard/using-linq)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e103-162">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="8e103-163">[AutoMapper](https://github.com/AutoMapper/AutoMapper) などのライブラリを使用して自動的に実行します。</span><span class="sxs-lookup"><span data-stu-id="8e103-163">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="8e103-164">次のサンプル アプリは、Ideas コントローラーの `Create` と `ForSession` API メソッドの単体テストを示します。</span><span class="sxs-lookup"><span data-stu-id="8e103-164">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="8e103-165">このサンプル アプリには、2 つの `ForSession` テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8e103-165">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="8e103-166">最初のテストでは、無効なセッションに対して `ForSession` が <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) を返すかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8e103-166">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="8e103-167">2 番目の `ForSession` テストでは、有効なセッションに対して `ForSession` がセッションのアイデアの一覧 (`<List<IdeaDTO>>`) を返すかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8e103-167">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="8e103-168">これらのチェックでは、最初のアイデアを調べて、その `Name` プロパティが正しいことも確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-168">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="8e103-169">`ModelState` が無効の場合の `Create` メソッドの動作をテストするため、サンプル アプリでは、テストの一部としてコントローラーにモデル エラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="8e103-169">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="8e103-170">単体テストでは、モデルの検証またはモデル バインドのテストを試さないでください。無効な `ModelState` に遭遇したときのアクション メソッドの動作だけをテストしてください。</span><span class="sxs-lookup"><span data-stu-id="8e103-170">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="8e103-171">`Create` の 2 番目のテストでは、リポジトリが `null` を返すことに依存しているため、`null` を返すようにモック リポジトリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-171">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="8e103-172">テスト データベース (メモリ内またはそれ以外の場所) を作成し、この結果を返すクエリを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8e103-172">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="8e103-173">サンプル コードに示すように、このテストは単一のステートメントで実行できます。</span><span class="sxs-lookup"><span data-stu-id="8e103-173">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="8e103-174">3 番目の `Create` テスト (`Create_ReturnsNewlyCreatedIdeaForSession`) では、リポジトリの `UpdateAsync` メソッドが呼び出されることを検証します。</span><span class="sxs-lookup"><span data-stu-id="8e103-174">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="8e103-175">モックが `Verifiable` で呼び出され、モック リポジトリの `Verify` メソッドが呼び出されて、検証可能なメソッドが実行されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-175">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="8e103-176">`UpdateAsync` メソッドがデータを保存したことを確認するのは、単体テストの役割ではありません。それは統合テストで実行できます。</span><span class="sxs-lookup"><span data-stu-id="8e103-176">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a><span data-ttu-id="8e103-177">テスト ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="8e103-177">Test ActionResult\<T></span></span>

<span data-ttu-id="8e103-178">ASP.NET Core 2.1 以降では、 [actionresult \<T> ](xref:web-api/action-return-types#actionresultt-type) () を使用して、 <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> から派生した型を返す `ActionResult` か、特定の型を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="8e103-178">In ASP.NET Core 2.1 or later, [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="8e103-179">サンプル アプリには、特定のセッション `id` に対して `List<IdeaDTO>` を返すメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8e103-179">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="8e103-180">セッションに `id` が存在しない場合、コントローラーは <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> を返します。</span><span class="sxs-lookup"><span data-stu-id="8e103-180">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="8e103-181">`ApiIdeasControllerTests`には、`ForSessionActionResult` コントローラーの 2 つのテストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8e103-181">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="8e103-182">最初のテストでは、コントローラーは `ActionResult` を返すが、存在しないセッション `id` の存在しないアイデアの一覧は返さないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-182">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="8e103-183">`ActionResult` の型は `ActionResult<List<IdeaDTO>>`。</span><span class="sxs-lookup"><span data-stu-id="8e103-183">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="8e103-184"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> は <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>。</span><span class="sxs-lookup"><span data-stu-id="8e103-184">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="8e103-185">有効なセッション `id` に対する 2 番目のテストでは、メソッドが以下を返すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-185">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="8e103-186">`List<IdeaDTO>` 型の `ActionResult`。</span><span class="sxs-lookup"><span data-stu-id="8e103-186">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="8e103-187">[Actionresult \<T> 。値](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)は `List<IdeaDTO>` 型です。</span><span class="sxs-lookup"><span data-stu-id="8e103-187">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="8e103-188">一覧の最初の項目は、モック セッションに格納されているアイデアと一致する有効なアイデア (`GetTestSession` の呼び出しによって取得)。</span><span class="sxs-lookup"><span data-stu-id="8e103-188">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="8e103-189">このサンプル アプリには、特定のセッションで新しい `Idea` を作成するメソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="8e103-189">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="8e103-190">コントローラーは以下を返します。</span><span class="sxs-lookup"><span data-stu-id="8e103-190">The controller returns:</span></span>

* <span data-ttu-id="8e103-191">無効なモデルに対する <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>。</span><span class="sxs-lookup"><span data-stu-id="8e103-191"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="8e103-192">セッションが存在しない場合は <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>。</span><span class="sxs-lookup"><span data-stu-id="8e103-192"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="8e103-193">セッションが新しいアイデアで更新された場合は <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>。</span><span class="sxs-lookup"><span data-stu-id="8e103-193"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="8e103-194">`ApiIdeasControllerTests` には、`CreateActionResult` の 3 つのテストが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e103-194">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="8e103-195">最初のテストでは、無効なモデルに対して <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> が返されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-195">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="8e103-196">2 番目のテストでは、セッションが存在しない場合は <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> が返されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-196">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="8e103-197">有効なセッション `id` に対しては、最後のテストで以下を確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-197">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="8e103-198">メソッドが `BrainstormSession` 型の `ActionResult` を返す。</span><span class="sxs-lookup"><span data-stu-id="8e103-198">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="8e103-199">[Actionresult \<T> 。結果](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*)は <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> です。</span><span class="sxs-lookup"><span data-stu-id="8e103-199">The [ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="8e103-200">`CreatedAtActionResult` は *201 Created* 応答に類似した `Location` ヘッダー付きの応答である。</span><span class="sxs-lookup"><span data-stu-id="8e103-200">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="8e103-201">[Actionresult \<T> 。値](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)は `BrainstormSession` 型です。</span><span class="sxs-lookup"><span data-stu-id="8e103-201">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="8e103-202">セッション `UpdateAsync(testSession)` を更新するモック 呼び出しが呼び出された。</span><span class="sxs-lookup"><span data-stu-id="8e103-202">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="8e103-203">`Verifiable` メソッド呼び出しは、アサーション内で `mockRepo.Verify()` を実行することでチェックされます。</span><span class="sxs-lookup"><span data-stu-id="8e103-203">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="8e103-204">セッションに対して 2 つの `Idea` オブジェクトが返された。</span><span class="sxs-lookup"><span data-stu-id="8e103-204">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="8e103-205">最後の項目 (`UpdateAsync` へのモック呼び出しによって追加された `Idea`) が、テスト中にセッションに追加された `newIdea` と一致する。</span><span class="sxs-lookup"><span data-stu-id="8e103-205">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8e103-206">[コントローラー](xref:mvc/controllers/actions)は、すべての ASP.NET Core MVC アプリで中心的な役割を担います。</span><span class="sxs-lookup"><span data-stu-id="8e103-206">[Controllers](xref:mvc/controllers/actions) play a central role in any ASP.NET Core MVC app.</span></span> <span data-ttu-id="8e103-207">そのため、コントローラーが意図するとおりに動作するという信頼が必要です。</span><span class="sxs-lookup"><span data-stu-id="8e103-207">As such, you should have confidence that controllers behave as intended.</span></span> <span data-ttu-id="8e103-208">自動テストによって、アプリが運用環境にデプロイされる前にエラーを検出できます。</span><span class="sxs-lookup"><span data-stu-id="8e103-208">Automated tests can detect errors before the app is deployed to a production environment.</span></span>

<span data-ttu-id="8e103-209">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8e103-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="unit-tests-of-controller-logic"></a><span data-ttu-id="8e103-210">コントローラー ロジックの単体テスト</span><span class="sxs-lookup"><span data-stu-id="8e103-210">Unit tests of controller logic</span></span>

<span data-ttu-id="8e103-211">[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)では、アプリの一部をインフラストラクチャや依存関係から切り離してテストすることが必要とされます。</span><span class="sxs-lookup"><span data-stu-id="8e103-211">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="8e103-212">コントローラー ロジックの単体テストを行うと、単一のアクションの内容のみがテストされ、その依存関係やフレームワーク自体の動作はテストされません。</span><span class="sxs-lookup"><span data-stu-id="8e103-212">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

<span data-ttu-id="8e103-213">コントローラーのアクションの単体テストは、コントローラーの動作に注目するように設定します。</span><span class="sxs-lookup"><span data-stu-id="8e103-213">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="8e103-214">コントローラーの単体テストでは、[フィルター](xref:mvc/controllers/filters)、[ルーティング](xref:fundamentals/routing)、[モデル バインド](xref:mvc/models/model-binding)などのシナリオは除外します。</span><span class="sxs-lookup"><span data-stu-id="8e103-214">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="8e103-215">まとまって要求に応答するコンポーネント間のインタラクションをカバーするテストは、*統合テスト*によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-215">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="8e103-216">統合テストの詳細については、「<xref:test/integration-tests>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e103-216">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="8e103-217">カスタム フィルターやルートを作成している場合は、コントローラーの特定のアクションに対するテストの一部としてではなく、単体テストを切り離して実行します。</span><span class="sxs-lookup"><span data-stu-id="8e103-217">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="8e103-218">コントローラーの単体テストを理解するために、次のサンプル アプリ内のコントローラーを確認してください。</span><span class="sxs-lookup"><span data-stu-id="8e103-218">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> <span data-ttu-id="8e103-219">Home コントローラーは、ブレーンストーミング セッションの一覧を表示し、POST 要求で新しいブレーンストーミング セッションを作成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="8e103-219">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="8e103-220">上記のコントローラーの説明:</span><span class="sxs-lookup"><span data-stu-id="8e103-220">The preceding controller:</span></span>

* <span data-ttu-id="8e103-221">[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に従います。</span><span class="sxs-lookup"><span data-stu-id="8e103-221">Follows the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="8e103-222">[依存関係の注入 (DI)](xref:fundamentals/dependency-injection) を予期して、`IBrainstormSessionRepository` のインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="8e103-222">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="8e103-223">モック オブジェクト フレームワークを使用するモックされた `IBrainstormSessionRepository` サービスでテストできます ([Moq](https://www.nuget.org/packages/Moq/) サービスなど)。</span><span class="sxs-lookup"><span data-stu-id="8e103-223">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="8e103-224">*モック オブジェクト*は、テストで使用されるプロパティとメソッドの動作が事前定義されている加工オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="8e103-224">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="8e103-225">詳細については、「[Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests)」(統合テストの概要) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e103-225">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="8e103-226">`HTTP GET Index` メソッドにはループや分岐はなく、1 つのメソッドを呼び出すだけです。</span><span class="sxs-lookup"><span data-stu-id="8e103-226">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="8e103-227">このアクションに対する単体テストでは、以下を行います。</span><span class="sxs-lookup"><span data-stu-id="8e103-227">The unit test for this action:</span></span>

* <span data-ttu-id="8e103-228">`IBrainstormSessionRepository` メソッドを使用する `GetTestSessions` サービスをモックする。</span><span class="sxs-lookup"><span data-stu-id="8e103-228">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="8e103-229">`GetTestSessions` が日付とセッション名を持つ 2 つのモック ブレーンストーミング セッションを作成する。</span><span class="sxs-lookup"><span data-stu-id="8e103-229">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="8e103-230">`Index` メソッドを実行する。</span><span class="sxs-lookup"><span data-stu-id="8e103-230">Executes the `Index` method.</span></span>
* <span data-ttu-id="8e103-231">メソッドによって返された結果に関するアサーションを行う。</span><span class="sxs-lookup"><span data-stu-id="8e103-231">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="8e103-232"><xref:Microsoft.AspNetCore.Mvc.ViewResult> が返される。</span><span class="sxs-lookup"><span data-stu-id="8e103-232">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="8e103-233">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) は `StormSessionViewModel`。</span><span class="sxs-lookup"><span data-stu-id="8e103-233">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="8e103-234">`ViewDataDictionary.Model`に格納された 2 つのブレーンストーミング セッションがある。</span><span class="sxs-lookup"><span data-stu-id="8e103-234">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="8e103-235">Home コントローラーの `HTTP POST Index` メソッドのテストでは、以下が検証されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-235">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="8e103-236">[ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) が `false` の場合、アクション メソッドは、*400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> と適切なデータを返す。</span><span class="sxs-lookup"><span data-stu-id="8e103-236">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="8e103-237">`ModelState.IsValid` が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="8e103-237">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="8e103-238">リポジトリの `Add` メソッドが呼び出される。</span><span class="sxs-lookup"><span data-stu-id="8e103-238">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="8e103-239"><xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> と適切な引数が返される。</span><span class="sxs-lookup"><span data-stu-id="8e103-239">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="8e103-240">下の最初のテストに示すように、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> を使用してエラーを追加することで、モデルの無効状態をテストできます。</span><span class="sxs-lookup"><span data-stu-id="8e103-240">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="8e103-241">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が有効でない場合は、GET 要求と同じ `ViewResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-241">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="8e103-242">このテストでは、無効なモデルを渡すことは試していません。</span><span class="sxs-lookup"><span data-stu-id="8e103-242">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="8e103-243">モデル バインドが実行されていないため、無効なモデルを渡すことは効果的なアプローチではありません (ただし、[統合テスト](xref:test/integration-tests)ではモデル バインドが使用されます)。</span><span class="sxs-lookup"><span data-stu-id="8e103-243">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="8e103-244">ここでは、モデル バインドはテストされていません。</span><span class="sxs-lookup"><span data-stu-id="8e103-244">In this case, model binding isn't tested.</span></span> <span data-ttu-id="8e103-245">これらの単体テストでは、アクション メソッドのコードだけをテストしています。</span><span class="sxs-lookup"><span data-stu-id="8e103-245">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="8e103-246">2 番目のテストでは、`ModelState` が有効な場合の検証を行います。</span><span class="sxs-lookup"><span data-stu-id="8e103-246">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="8e103-247">新しい `BrainstormSession` が追加される (リポジトリ経由)。</span><span class="sxs-lookup"><span data-stu-id="8e103-247">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="8e103-248">メソッドが `RedirectToActionResult` と予期されるプロパティを返す。</span><span class="sxs-lookup"><span data-stu-id="8e103-248">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="8e103-249">呼び出されないモックの呼び出しは通常は無視されますが、Setup 呼び出しの最後で `Verifiable` を呼び出すと、テスト内でのモック検証が許可されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-249">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="8e103-250">これは `mockRepo.Verify` の呼び出しで実行され、予期されるメソッドが呼び出されないとテストは失敗します。</span><span class="sxs-lookup"><span data-stu-id="8e103-250">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="8e103-251">このサンプルで使われている Moq ライブラリにより、検証可能な ("厳密な") モックと検証不可能なモック ("厳密でない" モックまたはスタブとも呼ばれます) を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="8e103-251">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="8e103-252">詳しくは、Moq の「[Customizing Mock Behavior](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)」(モックの動作のカスタマイズ) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8e103-252">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="8e103-253">サンプル アプリの [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) は、特定のブレーンストーミング セッションに関する情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="8e103-253">[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="8e103-254">このコントローラーには、無効な `id` 値を処理するロジックが含まれています (これらのシナリオをカバーするために、次のサンプルには 2 つの `return` シナリオがあります)。</span><span class="sxs-lookup"><span data-stu-id="8e103-254">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="8e103-255">最終的な `return` ステートメントにより、新しい `StormSessionViewModel` がビュー (*Controllers/SessionController.cs*) に返されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-255">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="8e103-256">Session コントローラーの `Index`アクション内に各 `return` シナリオ用の 1 つのテストを含む単体テスト:</span><span class="sxs-lookup"><span data-stu-id="8e103-256">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="8e103-257">Ideas コントローラーに移ると、アプリは、機能を Web API として `api/ideas` ルートで公開します。</span><span class="sxs-lookup"><span data-stu-id="8e103-257">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="8e103-258">ブレーンストーミング セッションに関連付けられたアイデアの一覧 (`IdeaDTO`) が、`ForSession` メソッドによって返される。</span><span class="sxs-lookup"><span data-stu-id="8e103-258">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="8e103-259">`Create` メソッドが新しいアイデアをセッションに追加する。</span><span class="sxs-lookup"><span data-stu-id="8e103-259">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="8e103-260">API の呼び出しでビジネス ドメイン エンティティを直接返すことは避けてください。</span><span class="sxs-lookup"><span data-stu-id="8e103-260">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="8e103-261">ドメイン エンティティ:</span><span class="sxs-lookup"><span data-stu-id="8e103-261">Domain entities:</span></span>

* <span data-ttu-id="8e103-262">多くの場合、クライアントが必要とするもの以上のデータを含みます。</span><span class="sxs-lookup"><span data-stu-id="8e103-262">Often include more data than the client requires.</span></span>
* <span data-ttu-id="8e103-263">アプリの内部ドメイン モデルと一般公開されている API が不必要に結合します。</span><span class="sxs-lookup"><span data-stu-id="8e103-263">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="8e103-264">ドメイン エンティティとクライアントに返される型の間のマッピングは、以下のように実行できます。</span><span class="sxs-lookup"><span data-stu-id="8e103-264">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="8e103-265">サンプル アプリで使用しているように、LINQ `Select` を使用して手動で実行します。</span><span class="sxs-lookup"><span data-stu-id="8e103-265">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="8e103-266">詳細については、「[LINQ (統合言語クエリ)](/dotnet/standard/using-linq)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e103-266">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="8e103-267">[AutoMapper](https://github.com/AutoMapper/AutoMapper) などのライブラリを使用して自動的に実行します。</span><span class="sxs-lookup"><span data-stu-id="8e103-267">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="8e103-268">次のサンプル アプリは、Ideas コントローラーの `Create` と `ForSession` API メソッドの単体テストを示します。</span><span class="sxs-lookup"><span data-stu-id="8e103-268">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="8e103-269">このサンプル アプリには、2 つの `ForSession` テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8e103-269">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="8e103-270">最初のテストでは、無効なセッションに対して `ForSession` が <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) を返すかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8e103-270">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="8e103-271">2 番目の `ForSession` テストでは、有効なセッションに対して `ForSession` がセッションのアイデアの一覧 (`<List<IdeaDTO>>`) を返すかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8e103-271">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="8e103-272">これらのチェックでは、最初のアイデアを調べて、その `Name` プロパティが正しいことも確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-272">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="8e103-273">`ModelState` が無効の場合の `Create` メソッドの動作をテストするため、サンプル アプリでは、テストの一部としてコントローラーにモデル エラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="8e103-273">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="8e103-274">単体テストでは、モデルの検証またはモデル バインドのテストを試さないでください。無効な `ModelState` に遭遇したときのアクション メソッドの動作だけをテストしてください。</span><span class="sxs-lookup"><span data-stu-id="8e103-274">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="8e103-275">`Create` の 2 番目のテストでは、リポジトリが `null` を返すことに依存しているため、`null` を返すようにモック リポジトリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="8e103-275">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="8e103-276">テスト データベース (メモリ内またはそれ以外の場所) を作成し、この結果を返すクエリを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8e103-276">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="8e103-277">サンプル コードに示すように、このテストは単一のステートメントで実行できます。</span><span class="sxs-lookup"><span data-stu-id="8e103-277">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="8e103-278">3 番目の `Create` テスト (`Create_ReturnsNewlyCreatedIdeaForSession`) では、リポジトリの `UpdateAsync` メソッドが呼び出されることを検証します。</span><span class="sxs-lookup"><span data-stu-id="8e103-278">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="8e103-279">モックが `Verifiable` で呼び出され、モック リポジトリの `Verify` メソッドが呼び出されて、検証可能なメソッドが実行されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-279">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="8e103-280">`UpdateAsync` メソッドがデータを保存したことを確認するのは、単体テストの役割ではありません。それは統合テストで実行できます。</span><span class="sxs-lookup"><span data-stu-id="8e103-280">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a><span data-ttu-id="8e103-281">テスト ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="8e103-281">Test ActionResult\<T></span></span>

<span data-ttu-id="8e103-282">ASP.NET Core 2.1 以降では、 [actionresult \<T> ](xref:web-api/action-return-types#actionresultt-type) () を使用して、 <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> から派生した型を返す `ActionResult` か、特定の型を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="8e103-282">In ASP.NET Core 2.1 or later, [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="8e103-283">サンプル アプリには、特定のセッション `id` に対して `List<IdeaDTO>` を返すメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8e103-283">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="8e103-284">セッションに `id` が存在しない場合、コントローラーは <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> を返します。</span><span class="sxs-lookup"><span data-stu-id="8e103-284">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="8e103-285">`ApiIdeasControllerTests`には、`ForSessionActionResult` コントローラーの 2 つのテストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8e103-285">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="8e103-286">最初のテストでは、コントローラーは `ActionResult` を返すが、存在しないセッション `id` の存在しないアイデアの一覧は返さないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-286">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="8e103-287">`ActionResult` の型は `ActionResult<List<IdeaDTO>>`。</span><span class="sxs-lookup"><span data-stu-id="8e103-287">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="8e103-288"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> は <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>。</span><span class="sxs-lookup"><span data-stu-id="8e103-288">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="8e103-289">有効なセッション `id` に対する 2 番目のテストでは、メソッドが以下を返すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-289">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="8e103-290">`List<IdeaDTO>` 型の `ActionResult`。</span><span class="sxs-lookup"><span data-stu-id="8e103-290">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="8e103-291">[Actionresult \<T> 。値](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)は `List<IdeaDTO>` 型です。</span><span class="sxs-lookup"><span data-stu-id="8e103-291">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="8e103-292">一覧の最初の項目は、モック セッションに格納されているアイデアと一致する有効なアイデア (`GetTestSession` の呼び出しによって取得)。</span><span class="sxs-lookup"><span data-stu-id="8e103-292">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="8e103-293">このサンプル アプリには、特定のセッションで新しい `Idea` を作成するメソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="8e103-293">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="8e103-294">コントローラーは以下を返します。</span><span class="sxs-lookup"><span data-stu-id="8e103-294">The controller returns:</span></span>

* <span data-ttu-id="8e103-295">無効なモデルに対する <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>。</span><span class="sxs-lookup"><span data-stu-id="8e103-295"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="8e103-296">セッションが存在しない場合は <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>。</span><span class="sxs-lookup"><span data-stu-id="8e103-296"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="8e103-297">セッションが新しいアイデアで更新された場合は <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>。</span><span class="sxs-lookup"><span data-stu-id="8e103-297"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="8e103-298">`ApiIdeasControllerTests` には、`CreateActionResult` の 3 つのテストが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e103-298">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="8e103-299">最初のテストでは、無効なモデルに対して <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> が返されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-299">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="8e103-300">2 番目のテストでは、セッションが存在しない場合は <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> が返されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-300">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="8e103-301">有効なセッション `id` に対しては、最後のテストで以下を確認します。</span><span class="sxs-lookup"><span data-stu-id="8e103-301">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="8e103-302">メソッドが `BrainstormSession` 型の `ActionResult` を返す。</span><span class="sxs-lookup"><span data-stu-id="8e103-302">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="8e103-303">[Actionresult \<T> 。結果](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*)は <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> です。</span><span class="sxs-lookup"><span data-stu-id="8e103-303">The [ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="8e103-304">`CreatedAtActionResult` は *201 Created* 応答に類似した `Location` ヘッダー付きの応答である。</span><span class="sxs-lookup"><span data-stu-id="8e103-304">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="8e103-305">[Actionresult \<T> 。値](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)は `BrainstormSession` 型です。</span><span class="sxs-lookup"><span data-stu-id="8e103-305">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="8e103-306">セッション `UpdateAsync(testSession)` を更新するモック 呼び出しが呼び出された。</span><span class="sxs-lookup"><span data-stu-id="8e103-306">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="8e103-307">`Verifiable` メソッド呼び出しは、アサーション内で `mockRepo.Verify()` を実行することでチェックされます。</span><span class="sxs-lookup"><span data-stu-id="8e103-307">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="8e103-308">セッションに対して 2 つの `Idea` オブジェクトが返された。</span><span class="sxs-lookup"><span data-stu-id="8e103-308">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="8e103-309">最後の項目 (`UpdateAsync` へのモック呼び出しによって追加された `Idea`) が、テスト中にセッションに追加された `newIdea` と一致する。</span><span class="sxs-lookup"><span data-stu-id="8e103-309">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8e103-310">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8e103-310">Additional resources</span></span>

* <xref:test/integration-tests>
* [<span data-ttu-id="8e103-311">Visual Studio で単体テストを作成して実行する</span><span class="sxs-lookup"><span data-stu-id="8e103-311">Create and run unit tests with Visual Studio</span></span>](/visualstudio/test/unit-test-your-code)
* <span data-ttu-id="8e103-312">[ASP.NET CORE mvc 用の AspNetCore-Fluent テストライブラリ](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): 厳密に型指定された単体テストライブラリ。 mvc および web API アプリをテストするための fluent インターフェイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="8e103-312">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="8e103-313">("*Microsoft では保守管理もサポートも行っていません。* ")</span><span class="sxs-lookup"><span data-stu-id="8e103-313">(*Not maintained or supported by Microsoft.*)</span></span>
* <span data-ttu-id="8e103-314">[JustMockLite](https://github.com/telerik/JustMockLite):.NET 開発者向けのモック フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="8e103-314">[JustMockLite](https://github.com/telerik/JustMockLite): A mocking framework for .NET developers.</span></span> <span data-ttu-id="8e103-315">("*Microsoft では保守管理もサポートも行っていません。* ")</span><span class="sxs-lookup"><span data-stu-id="8e103-315">(*Not maintained or supported by Microsoft.*)</span></span>

