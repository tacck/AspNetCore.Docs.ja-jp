---
title: ASP.NET Core での Razor Pages の単体テスト
author: rick-anderson
description: Razor Pages アプリの単体テストを作成する方法を学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: test/razor-pages-tests
ms.openlocfilehash: 2486eb8c9fd0fc33ea77b0fedd99795218d7f4ca
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058039"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="edb23-103">ASP.NET Core での Razor Pages の単体テスト</span><span class="sxs-lookup"><span data-stu-id="edb23-103">Razor Pages unit tests in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="edb23-104">ASP.NET Core では Razor Pages アプリの単体テストをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="edb23-104">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="edb23-105">データ アクセス層 (DAL) とページ モデルのテストによって、次のことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="edb23-105">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="edb23-106">Razor Pages アプリの一部は、アプリの構築時に独立して、または 1 つの単位として連携して機能します。</span><span class="sxs-lookup"><span data-stu-id="edb23-106">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="edb23-107">クラスとメソッドの担当のスコープは限定されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-107">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="edb23-108">アプリの動作に関する追加のドキュメントがあります。</span><span class="sxs-lookup"><span data-stu-id="edb23-108">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="edb23-109">コードの更新によって発生したエラーである回帰は、自動ビルドおよびデプロイ時に検出されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-109">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="edb23-110">このトピックでは、Razor Pages アプリと単体テストの基本的な知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="edb23-110">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="edb23-111">Razor Pages アプリまたはテストの概念になじみがない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="edb23-111">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="edb23-112">dotnet テストと xUnit を使用した .NET Core での単体テスト C#</span><span class="sxs-lookup"><span data-stu-id="edb23-112">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="edb23-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="edb23-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="edb23-114">サンプル プロジェクトは、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-114">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="edb23-115">アプリ</span><span class="sxs-lookup"><span data-stu-id="edb23-115">App</span></span>         | <span data-ttu-id="edb23-116">プロジェクト フォルダー</span><span class="sxs-lookup"><span data-stu-id="edb23-116">Project folder</span></span>                     | <span data-ttu-id="edb23-117">説明</span><span class="sxs-lookup"><span data-stu-id="edb23-117">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="edb23-118">メッセージ アプリ</span><span class="sxs-lookup"><span data-stu-id="edb23-118">Message app</span></span> | <span data-ttu-id="edb23-119">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="edb23-119">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="edb23-120">ユーザーがメッセージの追加、1 つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析 (メッセージあたりの平均単語数の検出) をできるようにします。</span><span class="sxs-lookup"><span data-stu-id="edb23-120">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="edb23-121">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="edb23-121">Test app</span></span>    | <span data-ttu-id="edb23-122">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="edb23-122">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="edb23-123">メッセージ アプリの DAL およびインデックス ページ モデルの単体テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-123">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="edb23-124">IDE の組み込みテスト機能 ([Visual Studio](/visualstudio/test/unit-test-your-code) や [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) など) を使用して、テストを実行できます。</span><span class="sxs-lookup"><span data-stu-id="edb23-124">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="edb23-125">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用する場合は、コマンドプロンプトで *tests/RazorPagesTestSample.Tests* フォルダー内の次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="edb23-125">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="edb23-126">メッセージ アプリの構成</span><span class="sxs-lookup"><span data-stu-id="edb23-126">Message app organization</span></span>

<span data-ttu-id="edb23-127">メッセージ アプリは、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="edb23-127">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="edb23-128">アプリのインデックス ページ (*Pages/Index. cshtml* と *Pages/Index. cshtml*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数の検出) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-128">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="edb23-129">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-129">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="edb23-130">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-130">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="edb23-131">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-131">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="edb23-132">アプリでは、データベース コンテキスト クラス `AppDbContext` (*Data/AppDbContext.cs*) 内に DAL を格納します。</span><span class="sxs-lookup"><span data-stu-id="edb23-132">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="edb23-133">DAL メソッドは `virtual` とマークされ、これにより、テストで使用するためのメソッドのモックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="edb23-133">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="edb23-134">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-134">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="edb23-135">これらの *シードされたメッセージ* も、テストで使用されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-135">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="edb23-136">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="edb23-136">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="edb23-137">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="edb23-137">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="edb23-138">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="edb23-138">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="edb23-139">サンプル アプリではリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="edb23-139">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="edb23-140">詳細については、[インフラストラクチャの永続レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)に関するページと「<xref:mvc/controllers/testing>」を参照してください (このサンプルでは、リポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="edb23-140">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="edb23-141">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="edb23-141">Test app organization</span></span>

<span data-ttu-id="edb23-142">テスト アプリは、*tests/RazorPagesTestSample.Tests* フォルダー内のコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="edb23-142">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="edb23-143">テスト アプリ フォルダー</span><span class="sxs-lookup"><span data-stu-id="edb23-143">Test app folder</span></span> | <span data-ttu-id="edb23-144">説明</span><span class="sxs-lookup"><span data-stu-id="edb23-144">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="edb23-145">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="edb23-145">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="edb23-146">*DataAccessLayerTest.cs* には、DAL の単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="edb23-146">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="edb23-147">*IndexPageTests.cs* には、インデックス ページ モデルの単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="edb23-147">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="edb23-148">*Utilities*</span><span class="sxs-lookup"><span data-stu-id="edb23-148">*Utilities*</span></span>     | <span data-ttu-id="edb23-149">データベースが各テストのベースライン条件にリセットされるように、各 DAL 単体テストの新しいデータベース コンテキスト オプションを作成するために使用する `TestDbContextOptions` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="edb23-149">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="edb23-150">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="edb23-150">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="edb23-151">オブジェクトのモック フレームワークは [Moq](https://github.com/moq/moq4) です。</span><span class="sxs-lookup"><span data-stu-id="edb23-151">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="edb23-152">データアクセス層 (DAL) の単体テスト</span><span class="sxs-lookup"><span data-stu-id="edb23-152">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="edb23-153">メッセージ アプリには、`AppDbContext` クラス (*src/RazorPagesTestSample/Data/AppDbContext.cs*) に含まれる 4 つのメソッドを持つ DAL があります。</span><span class="sxs-lookup"><span data-stu-id="edb23-153">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="edb23-154">テスト アプリで、各メソッドには 1 つか 2 つの単体テストがあります。</span><span class="sxs-lookup"><span data-stu-id="edb23-154">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="edb23-155">DAL メソッド</span><span class="sxs-lookup"><span data-stu-id="edb23-155">DAL method</span></span>               | <span data-ttu-id="edb23-156">関数</span><span class="sxs-lookup"><span data-stu-id="edb23-156">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="edb23-157">データベースから `Text` プロパティで並べ替えられた `List<Message>` を取得します。</span><span class="sxs-lookup"><span data-stu-id="edb23-157">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="edb23-158">`Message` をデータベースに追加します。</span><span class="sxs-lookup"><span data-stu-id="edb23-158">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="edb23-159">データベースからすべての `Message` エントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="edb23-159">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="edb23-160">`Id` によって、データベースから 1 つの `Message` を削除します。</span><span class="sxs-lookup"><span data-stu-id="edb23-160">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="edb23-161">DAL の単体テストでは、各テストの新しい `AppDbContext` を作成する際に、<xref:Microsoft.EntityFrameworkCore.DbContextOptions> が必要です。</span><span class="sxs-lookup"><span data-stu-id="edb23-161">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="edb23-162">各テストの `DbContextOptions` を作成する 1 つの方法として、<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> を使用します。</span><span class="sxs-lookup"><span data-stu-id="edb23-162">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="edb23-163">この方法の問題は、各テストでは、前のテストでデータベースがどのような状態になっていても、それを受け取るということです。</span><span class="sxs-lookup"><span data-stu-id="edb23-163">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="edb23-164">相互に干渉しないアトミック単体テストを作成しようとする場合に、これが問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="edb23-164">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="edb23-165">`AppDbContext` でテストごとに新しいデータベース コンテキストを強制的に使用させるには、新しいサービス プロバイダーに基づいた `DbContextOptions` インスタンスを指定します。</span><span class="sxs-lookup"><span data-stu-id="edb23-165">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="edb23-166">テスト アプリでは、その `Utilities` クラス メソッド `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*) を使用してこれを行う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="edb23-166">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="edb23-167">DAL 単体テストで `DbContextOptions` を使用すると、新しいデータベース インスタンスを使用して、各テストをアトミックに実行できます。</span><span class="sxs-lookup"><span data-stu-id="edb23-167">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="edb23-168">`DataAccessLayerTest` クラスの各テスト メソッド (*UnitTests/DataAccessLayerTest.cs*) は、同様の Arrange-Act-Assert パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="edb23-168">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="edb23-169">Arrange (環境構築):データベースがテスト用に構成され、期待される結果が定義されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-169">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="edb23-170">Act (実行):テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-170">Act: The test is executed.</span></span>
1. <span data-ttu-id="edb23-171">Assert (動作確認):アサーションによって、テスト結果が成功であるかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-171">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="edb23-172">たとえば、`DeleteMessageAsync` メソッドは、`Id` によって識別された 1 つのメッセージの削除を担当します (*src/RazorPagesTestSample/Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="edb23-172">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="edb23-173">このメソッドには 2 つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="edb23-173">There are two tests for this method.</span></span> <span data-ttu-id="edb23-174">1 つのテストでは、メッセージがデータベースに存在する場合に、このメソッドによってメッセージが削除されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-174">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="edb23-175">他方のメソッドでは、削除対象のメッセージ `Id` が存在しない場合に、データベースが変更されないことをテストします。</span><span class="sxs-lookup"><span data-stu-id="edb23-175">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="edb23-176">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` メソッドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="edb23-176">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="edb23-177">まず、このメソッドでは、Arrange ステップを実行し、Act ステップの準備を行います。</span><span class="sxs-lookup"><span data-stu-id="edb23-177">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="edb23-178">シード処理メッセージが取得され、`seedMessages` で保持されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-178">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="edb23-179">シード処理メッセージはデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-179">The seeding messages are saved into the database.</span></span> <span data-ttu-id="edb23-180">`Id` が `1` のメッセージは、削除対象として設定されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-180">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="edb23-181">`DeleteMessageAsync` メソッドが実行されると、予期されるメッセージには、`Id` が `1` のメッセージを除くすべてのメッセージが含まれるはずです。</span><span class="sxs-lookup"><span data-stu-id="edb23-181">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="edb23-182">`expectedMessages` 変数は、この予期される結果を表します。</span><span class="sxs-lookup"><span data-stu-id="edb23-182">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="edb23-183">メソッドは次のように動作します。`DeleteMessageAsync` メソッドは `1`の `recId` を渡して実行されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-183">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="edb23-184">最後に、メソッドはコンテキストから `Messages` を取得し、`expectedMessages` と比較して、2 つが等しいことをアサートします。</span><span class="sxs-lookup"><span data-stu-id="edb23-184">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="edb23-185">2 つの `List<Message>` が同じであることを比較するために:</span><span class="sxs-lookup"><span data-stu-id="edb23-185">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="edb23-186">メッセージは `Id` 順に並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="edb23-186">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="edb23-187">メッセージのペアは、`Text` プロパティで比較されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-187">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="edb23-188">類似のテスト メソッド `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` では、存在しないメッセージを削除しようとした結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="edb23-188">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="edb23-189">この場合、データベース内の予期されるメッセージは、`DeleteMessageAsync` メソッドが実行された後の実際のメッセージと等しくなるはずです。</span><span class="sxs-lookup"><span data-stu-id="edb23-189">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="edb23-190">データベースのコンテンツへの変更はないはずです。</span><span class="sxs-lookup"><span data-stu-id="edb23-190">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="edb23-191">ページ モデル メソッドの単体テスト</span><span class="sxs-lookup"><span data-stu-id="edb23-191">Unit tests of the page model methods</span></span>

<span data-ttu-id="edb23-192">別の単体テスト セットは、ページ モデル メソッドのテストを担当します。</span><span class="sxs-lookup"><span data-stu-id="edb23-192">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="edb23-193">メッセージ アプリで、インデックス ページ モデルは *src/RazorPagesTestSample/Pages/Index.cshtml.cs* の `IndexModel` クラスにあります。</span><span class="sxs-lookup"><span data-stu-id="edb23-193">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="edb23-194">ページ モデル メソッド</span><span class="sxs-lookup"><span data-stu-id="edb23-194">Page model method</span></span> | <span data-ttu-id="edb23-195">関数</span><span class="sxs-lookup"><span data-stu-id="edb23-195">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="edb23-196">`GetMessagesAsync` メソッドを使用して、UI の DAL からメッセージを取得します。</span><span class="sxs-lookup"><span data-stu-id="edb23-196">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="edb23-197">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が有効な場合、`AddMessageAsync` を呼び出して、データベースにメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="edb23-197">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="edb23-198">データベース内のすべてのメッセージを削除するには、`DeleteAllMessagesAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="edb23-198">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="edb23-199">`Id` を指定してメッセージを削除するには、`DeleteMessageAsync` を実行します。</span><span class="sxs-lookup"><span data-stu-id="edb23-199">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="edb23-200">データベースに 1 つ以上のメッセージが含まれている場合、メッセージあたりの平均単語数を計算します。</span><span class="sxs-lookup"><span data-stu-id="edb23-200">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="edb23-201">ページ モデル メソッドは、`IndexPageTests` クラス (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*) の 7 つのテストを使用してテストされます。</span><span class="sxs-lookup"><span data-stu-id="edb23-201">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="edb23-202">テストでは、おなじみの Arrange-Assert-Act パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="edb23-202">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="edb23-203">これらのテストでは次に焦点を合わせています。</span><span class="sxs-lookup"><span data-stu-id="edb23-203">These tests focus on:</span></span>

* <span data-ttu-id="edb23-204">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="edb23-204">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="edb23-205">メソッドによって正しい <xref:Microsoft.AspNetCore.Mvc.IActionResult> が生成されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="edb23-205">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="edb23-206">プロパティ値の割り当てが正しく行われていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="edb23-206">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="edb23-207">この一連のテストでは、多くの場合に、ページ モデル メソッドが実行される Act ステップ用に予期されるデータを生成するために、DAL のメソッドのモックを作成します。</span><span class="sxs-lookup"><span data-stu-id="edb23-207">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="edb23-208">たとえば、`AppDbContext` の `GetMessagesAsync` メソッドは、出力を生成するためにモックが作成されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-208">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="edb23-209">ページ モデル メソッドでこのメソッドを実行すると、モックによって結果が返されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-209">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="edb23-210">データはデータベースから取得されません。</span><span class="sxs-lookup"><span data-stu-id="edb23-210">The data doesn't come from the database.</span></span> <span data-ttu-id="edb23-211">これにより、ページ モデル テストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-211">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="edb23-212">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` テストでは、ページ モデルに対して `GetMessagesAsync` メソッドのモックを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="edb23-212">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="edb23-213">Act ステップで `OnGetAsync` メソッドが実行されると、ページ モデルの `GetMessagesAsync` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-213">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="edb23-214">単体テストの Act ステップ (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span><span class="sxs-lookup"><span data-stu-id="edb23-214">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="edb23-215">`IndexPage` ページ モデルの `OnGetAsync` メソッド (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="edb23-215">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="edb23-216">DAL の `GetMessagesAsync` メソッドでは、このメソッド呼び出しの結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="edb23-216">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="edb23-217">メソッドのモック バージョンで、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="edb23-217">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="edb23-218">`Assert` ステップでは、実際のメッセージ (`actualMessages`) がページ モデルの `Messages` プロパティから割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="edb23-218">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="edb23-219">メッセージが割り当てられるときに、型チェックも実行されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-219">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="edb23-220">予期されるメッセージと実際のメッセージが、それらの `Text` プロパティによって比較されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-220">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="edb23-221">テストでは、2 つの `List<Message>` インスタンスに同じメッセージが含まれていることをアサートします。</span><span class="sxs-lookup"><span data-stu-id="edb23-221">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="edb23-222">このグループの他のテストでは、<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>、`PageContext` を確立するための <xref:Microsoft.AspNetCore.Mvc.ActionContext>、`ViewDataDictionary`、および `PageContext` を含むページ モデル オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-222">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="edb23-223">これらは、テストの実施に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="edb23-223">These are useful in conducting tests.</span></span> <span data-ttu-id="edb23-224">たとえば、メッセージ アプリでは、`OnPostAddMessageAsync` の実行時に有効な <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> が返されることを確認するために、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> を使用して `ModelState` エラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="edb23-224">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="edb23-225">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="edb23-225">Additional resources</span></span>

* [<span data-ttu-id="edb23-226">dotnet テストと xUnit を使用した .NET Core での単体テスト C#</span><span class="sxs-lookup"><span data-stu-id="edb23-226">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="edb23-227">[コードの単体テスト](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="edb23-227">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="edb23-228">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="edb23-228">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="edb23-229">Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築</span><span class="sxs-lookup"><span data-stu-id="edb23-229">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="edb23-230">XUnit.net の概要:.NET SDK コマンド ラインでの .Net Core の使用</span><span class="sxs-lookup"><span data-stu-id="edb23-230">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="edb23-231">Moq</span><span class="sxs-lookup"><span data-stu-id="edb23-231">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="edb23-232">Moq クイック スタート</span><span class="sxs-lookup"><span data-stu-id="edb23-232">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="edb23-233">ASP.NET Core では Razor Pages アプリの単体テストをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="edb23-233">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="edb23-234">データ アクセス層 (DAL) とページ モデルのテストによって、次のことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="edb23-234">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="edb23-235">Razor Pages アプリの一部は、アプリの構築時に独立して、または 1 つの単位として連携して機能します。</span><span class="sxs-lookup"><span data-stu-id="edb23-235">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="edb23-236">クラスとメソッドの担当のスコープは限定されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-236">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="edb23-237">アプリの動作に関する追加のドキュメントがあります。</span><span class="sxs-lookup"><span data-stu-id="edb23-237">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="edb23-238">コードの更新によって発生したエラーである回帰は、自動ビルドおよびデプロイ時に検出されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-238">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="edb23-239">このトピックでは、Razor Pages アプリと単体テストの基本的な知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="edb23-239">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="edb23-240">Razor Pages アプリまたはテストの概念になじみがない場合は、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="edb23-240">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="edb23-241">dotnet テストと xUnit を使用した .NET Core での単体テスト C#</span><span class="sxs-lookup"><span data-stu-id="edb23-241">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="edb23-242">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="edb23-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="edb23-243">サンプル プロジェクトは、次の 2 つのアプリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-243">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="edb23-244">アプリ</span><span class="sxs-lookup"><span data-stu-id="edb23-244">App</span></span>         | <span data-ttu-id="edb23-245">プロジェクト フォルダー</span><span class="sxs-lookup"><span data-stu-id="edb23-245">Project folder</span></span>                     | <span data-ttu-id="edb23-246">説明</span><span class="sxs-lookup"><span data-stu-id="edb23-246">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="edb23-247">メッセージ アプリ</span><span class="sxs-lookup"><span data-stu-id="edb23-247">Message app</span></span> | <span data-ttu-id="edb23-248">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="edb23-248">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="edb23-249">ユーザーがメッセージの追加、1 つのメッセージの削除、すべてのメッセージの削除、およびメッセージの分析 (メッセージあたりの平均単語数の検出) をできるようにします。</span><span class="sxs-lookup"><span data-stu-id="edb23-249">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="edb23-250">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="edb23-250">Test app</span></span>    | <span data-ttu-id="edb23-251">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="edb23-251">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="edb23-252">メッセージ アプリの DAL およびインデックス ページ モデルの単体テストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-252">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="edb23-253">IDE の組み込みテスト機能 ([Visual Studio](/visualstudio/test/unit-test-your-code) や [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) など) を使用して、テストを実行できます。</span><span class="sxs-lookup"><span data-stu-id="edb23-253">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="edb23-254">[Visual Studio Code](https://code.visualstudio.com/) またはコマンド ラインを使用する場合は、コマンドプロンプトで *tests/RazorPagesTestSample.Tests* フォルダー内の次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="edb23-254">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="edb23-255">メッセージ アプリの構成</span><span class="sxs-lookup"><span data-stu-id="edb23-255">Message app organization</span></span>

<span data-ttu-id="edb23-256">メッセージ アプリは、次の特性を持つ Razor Pages メッセージ システムです。</span><span class="sxs-lookup"><span data-stu-id="edb23-256">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="edb23-257">アプリのインデックス ページ (*Pages/Index. cshtml* と *Pages/Index. cshtml*) には、メッセージの追加、削除、および分析 (メッセージあたりの平均単語数の検出) を制御する UI およびページ モデル メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-257">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="edb23-258">メッセージは、`Id` (キー) と `Text` (メッセージ) の 2 つのプロパティを持つ `Message` クラス (*Data/Message.cs*) によって記述されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-258">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="edb23-259">`Text` プロパティは必須であり、200 文字までに制限されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-259">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="edb23-260">メッセージは [Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224; を使用して格納されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-260">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="edb23-261">アプリでは、データベース コンテキスト クラス `AppDbContext` (*Data/AppDbContext.cs*) 内に DAL を格納します。</span><span class="sxs-lookup"><span data-stu-id="edb23-261">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="edb23-262">DAL メソッドは `virtual` とマークされ、これにより、テストで使用するためのメソッドのモックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="edb23-262">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="edb23-263">アプリの起動時にデータベースが空の場合、メッセージ ストアが 3 つのメッセージで初期化されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-263">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="edb23-264">これらの *シードされたメッセージ* も、テストで使用されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-264">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="edb23-265">&#8224; EF トピック「[InMemory を使用したテスト](/ef/core/miscellaneous/testing/in-memory)」では、MSTest を使用したテストでメモリ内データベースを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="edb23-265">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="edb23-266">このトピックでは、[xUnit](https://xunit.github.io/) テスト フレームワークを使用します。</span><span class="sxs-lookup"><span data-stu-id="edb23-266">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="edb23-267">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="edb23-267">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="edb23-268">サンプル アプリではリポジトリ パターンを使用しておらず、[Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)の有効な例ではありませんが、Razor Pages ではこれらの開発パターンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="edb23-268">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="edb23-269">詳細については、[インフラストラクチャの永続レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)に関するページと「<xref:mvc/controllers/testing>」を参照してください (このサンプルでは、リポジトリ パターンを実装しています)。</span><span class="sxs-lookup"><span data-stu-id="edb23-269">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="edb23-270">テスト アプリの構成</span><span class="sxs-lookup"><span data-stu-id="edb23-270">Test app organization</span></span>

<span data-ttu-id="edb23-271">テスト アプリは、*tests/RazorPagesTestSample.Tests* フォルダー内のコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="edb23-271">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="edb23-272">テスト アプリ フォルダー</span><span class="sxs-lookup"><span data-stu-id="edb23-272">Test app folder</span></span> | <span data-ttu-id="edb23-273">説明</span><span class="sxs-lookup"><span data-stu-id="edb23-273">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="edb23-274">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="edb23-274">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="edb23-275">*DataAccessLayerTest.cs* には、DAL の単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="edb23-275">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="edb23-276">*IndexPageTests.cs* には、インデックス ページ モデルの単体テストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="edb23-276">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="edb23-277">*Utilities*</span><span class="sxs-lookup"><span data-stu-id="edb23-277">*Utilities*</span></span>     | <span data-ttu-id="edb23-278">データベースが各テストのベースライン条件にリセットされるように、各 DAL 単体テストの新しいデータベース コンテキスト オプションを作成するために使用する `TestDbContextOptions` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="edb23-278">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="edb23-279">テスト フレームワークは、[xUnit](https://xunit.github.io/) です。</span><span class="sxs-lookup"><span data-stu-id="edb23-279">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="edb23-280">オブジェクトのモック フレームワークは [Moq](https://github.com/moq/moq4) です。</span><span class="sxs-lookup"><span data-stu-id="edb23-280">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="edb23-281">データアクセス層 (DAL) の単体テスト</span><span class="sxs-lookup"><span data-stu-id="edb23-281">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="edb23-282">メッセージ アプリには、`AppDbContext` クラス (*src/RazorPagesTestSample/Data/AppDbContext.cs*) に含まれる 4 つのメソッドを持つ DAL があります。</span><span class="sxs-lookup"><span data-stu-id="edb23-282">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="edb23-283">テスト アプリで、各メソッドには 1 つか 2 つの単体テストがあります。</span><span class="sxs-lookup"><span data-stu-id="edb23-283">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="edb23-284">DAL メソッド</span><span class="sxs-lookup"><span data-stu-id="edb23-284">DAL method</span></span>               | <span data-ttu-id="edb23-285">関数</span><span class="sxs-lookup"><span data-stu-id="edb23-285">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="edb23-286">データベースから `Text` プロパティで並べ替えられた `List<Message>` を取得します。</span><span class="sxs-lookup"><span data-stu-id="edb23-286">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="edb23-287">`Message` をデータベースに追加します。</span><span class="sxs-lookup"><span data-stu-id="edb23-287">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="edb23-288">データベースからすべての `Message` エントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="edb23-288">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="edb23-289">`Id` によって、データベースから 1 つの `Message` を削除します。</span><span class="sxs-lookup"><span data-stu-id="edb23-289">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="edb23-290">DAL の単体テストでは、各テストの新しい `AppDbContext` を作成する際に、<xref:Microsoft.EntityFrameworkCore.DbContextOptions> が必要です。</span><span class="sxs-lookup"><span data-stu-id="edb23-290">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="edb23-291">各テストの `DbContextOptions` を作成する 1 つの方法として、<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> を使用します。</span><span class="sxs-lookup"><span data-stu-id="edb23-291">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="edb23-292">この方法の問題は、各テストでは、前のテストでデータベースがどのような状態になっていても、それを受け取るということです。</span><span class="sxs-lookup"><span data-stu-id="edb23-292">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="edb23-293">相互に干渉しないアトミック単体テストを作成しようとする場合に、これが問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="edb23-293">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="edb23-294">`AppDbContext` でテストごとに新しいデータベース コンテキストを強制的に使用させるには、新しいサービス プロバイダーに基づいた `DbContextOptions` インスタンスを指定します。</span><span class="sxs-lookup"><span data-stu-id="edb23-294">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="edb23-295">テスト アプリでは、その `Utilities` クラス メソッド `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*) を使用してこれを行う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="edb23-295">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="edb23-296">DAL 単体テストで `DbContextOptions` を使用すると、新しいデータベース インスタンスを使用して、各テストをアトミックに実行できます。</span><span class="sxs-lookup"><span data-stu-id="edb23-296">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="edb23-297">`DataAccessLayerTest` クラスの各テスト メソッド (*UnitTests/DataAccessLayerTest.cs*) は、同様の Arrange-Act-Assert パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="edb23-297">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="edb23-298">Arrange (環境構築):データベースがテスト用に構成され、期待される結果が定義されています。</span><span class="sxs-lookup"><span data-stu-id="edb23-298">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="edb23-299">Act (実行):テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-299">Act: The test is executed.</span></span>
1. <span data-ttu-id="edb23-300">Assert (動作確認):アサーションによって、テスト結果が成功であるかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-300">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="edb23-301">たとえば、`DeleteMessageAsync` メソッドは、`Id` によって識別された 1 つのメッセージの削除を担当します (*src/RazorPagesTestSample/Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="edb23-301">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="edb23-302">このメソッドには 2 つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="edb23-302">There are two tests for this method.</span></span> <span data-ttu-id="edb23-303">1 つのテストでは、メッセージがデータベースに存在する場合に、このメソッドによってメッセージが削除されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-303">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="edb23-304">他方のメソッドでは、削除対象のメッセージ `Id` が存在しない場合に、データベースが変更されないことをテストします。</span><span class="sxs-lookup"><span data-stu-id="edb23-304">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="edb23-305">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` メソッドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="edb23-305">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="edb23-306">まず、このメソッドでは、Arrange ステップを実行し、Act ステップの準備を行います。</span><span class="sxs-lookup"><span data-stu-id="edb23-306">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="edb23-307">シード処理メッセージが取得され、`seedMessages` で保持されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-307">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="edb23-308">シード処理メッセージはデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-308">The seeding messages are saved into the database.</span></span> <span data-ttu-id="edb23-309">`Id` が `1` のメッセージは、削除対象として設定されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-309">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="edb23-310">`DeleteMessageAsync` メソッドが実行されると、予期されるメッセージには、`Id` が `1` のメッセージを除くすべてのメッセージが含まれるはずです。</span><span class="sxs-lookup"><span data-stu-id="edb23-310">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="edb23-311">`expectedMessages` 変数は、この予期される結果を表します。</span><span class="sxs-lookup"><span data-stu-id="edb23-311">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="edb23-312">メソッドは次のように動作します。`DeleteMessageAsync` メソッドは `1`の `recId` を渡して実行されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-312">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="edb23-313">最後に、メソッドはコンテキストから `Messages` を取得し、`expectedMessages` と比較して、2 つが等しいことをアサートします。</span><span class="sxs-lookup"><span data-stu-id="edb23-313">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="edb23-314">2 つの `List<Message>` が同じであることを比較するために:</span><span class="sxs-lookup"><span data-stu-id="edb23-314">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="edb23-315">メッセージは `Id` 順に並べ替えられます。</span><span class="sxs-lookup"><span data-stu-id="edb23-315">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="edb23-316">メッセージのペアは、`Text` プロパティで比較されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-316">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="edb23-317">類似のテスト メソッド `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` では、存在しないメッセージを削除しようとした結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="edb23-317">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="edb23-318">この場合、データベース内の予期されるメッセージは、`DeleteMessageAsync` メソッドが実行された後の実際のメッセージと等しくなるはずです。</span><span class="sxs-lookup"><span data-stu-id="edb23-318">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="edb23-319">データベースのコンテンツへの変更はないはずです。</span><span class="sxs-lookup"><span data-stu-id="edb23-319">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="edb23-320">ページ モデル メソッドの単体テスト</span><span class="sxs-lookup"><span data-stu-id="edb23-320">Unit tests of the page model methods</span></span>

<span data-ttu-id="edb23-321">別の単体テスト セットは、ページ モデル メソッドのテストを担当します。</span><span class="sxs-lookup"><span data-stu-id="edb23-321">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="edb23-322">メッセージ アプリで、インデックス ページ モデルは *src/RazorPagesTestSample/Pages/Index.cshtml.cs* の `IndexModel` クラスにあります。</span><span class="sxs-lookup"><span data-stu-id="edb23-322">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="edb23-323">ページ モデル メソッド</span><span class="sxs-lookup"><span data-stu-id="edb23-323">Page model method</span></span> | <span data-ttu-id="edb23-324">関数</span><span class="sxs-lookup"><span data-stu-id="edb23-324">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="edb23-325">`GetMessagesAsync` メソッドを使用して、UI の DAL からメッセージを取得します。</span><span class="sxs-lookup"><span data-stu-id="edb23-325">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="edb23-326">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が有効な場合、`AddMessageAsync` を呼び出して、データベースにメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="edb23-326">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="edb23-327">データベース内のすべてのメッセージを削除するには、`DeleteAllMessagesAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="edb23-327">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="edb23-328">`Id` を指定してメッセージを削除するには、`DeleteMessageAsync` を実行します。</span><span class="sxs-lookup"><span data-stu-id="edb23-328">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="edb23-329">データベースに 1 つ以上のメッセージが含まれている場合、メッセージあたりの平均単語数を計算します。</span><span class="sxs-lookup"><span data-stu-id="edb23-329">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="edb23-330">ページ モデル メソッドは、`IndexPageTests` クラス (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*) の 7 つのテストを使用してテストされます。</span><span class="sxs-lookup"><span data-stu-id="edb23-330">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="edb23-331">テストでは、おなじみの Arrange-Assert-Act パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="edb23-331">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="edb23-332">これらのテストでは次に焦点を合わせています。</span><span class="sxs-lookup"><span data-stu-id="edb23-332">These tests focus on:</span></span>

* <span data-ttu-id="edb23-333">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) が無効な場合に、メソッドが正しい動作に従うかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="edb23-333">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="edb23-334">メソッドによって正しい <xref:Microsoft.AspNetCore.Mvc.IActionResult> が生成されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="edb23-334">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="edb23-335">プロパティ値の割り当てが正しく行われていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="edb23-335">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="edb23-336">この一連のテストでは、多くの場合に、ページ モデル メソッドが実行される Act ステップ用に予期されるデータを生成するために、DAL のメソッドのモックを作成します。</span><span class="sxs-lookup"><span data-stu-id="edb23-336">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="edb23-337">たとえば、`AppDbContext` の `GetMessagesAsync` メソッドは、出力を生成するためにモックが作成されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-337">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="edb23-338">ページ モデル メソッドでこのメソッドを実行すると、モックによって結果が返されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-338">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="edb23-339">データはデータベースから取得されません。</span><span class="sxs-lookup"><span data-stu-id="edb23-339">The data doesn't come from the database.</span></span> <span data-ttu-id="edb23-340">これにより、ページ モデル テストで DAL を使用するための、予測可能で信頼性の高いテスト条件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-340">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="edb23-341">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` テストでは、ページ モデルに対して `GetMessagesAsync` メソッドのモックを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="edb23-341">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="edb23-342">Act ステップで `OnGetAsync` メソッドが実行されると、ページ モデルの `GetMessagesAsync` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-342">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="edb23-343">単体テストの Act ステップ (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span><span class="sxs-lookup"><span data-stu-id="edb23-343">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="edb23-344">`IndexPage` ページ モデルの `OnGetAsync` メソッド (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="edb23-344">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="edb23-345">DAL の `GetMessagesAsync` メソッドでは、このメソッド呼び出しの結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="edb23-345">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="edb23-346">メソッドのモック バージョンで、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="edb23-346">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="edb23-347">`Assert` ステップでは、実際のメッセージ (`actualMessages`) がページ モデルの `Messages` プロパティから割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="edb23-347">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="edb23-348">メッセージが割り当てられるときに、型チェックも実行されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-348">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="edb23-349">予期されるメッセージと実際のメッセージが、それらの `Text` プロパティによって比較されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-349">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="edb23-350">テストでは、2 つの `List<Message>` インスタンスに同じメッセージが含まれていることをアサートします。</span><span class="sxs-lookup"><span data-stu-id="edb23-350">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="edb23-351">このグループの他のテストでは、<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>、`PageContext` を確立するための <xref:Microsoft.AspNetCore.Mvc.ActionContext>、`ViewDataDictionary`、および `PageContext` を含むページ モデル オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="edb23-351">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="edb23-352">これらは、テストの実施に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="edb23-352">These are useful in conducting tests.</span></span> <span data-ttu-id="edb23-353">たとえば、メッセージ アプリでは、`OnPostAddMessageAsync` の実行時に有効な <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> が返されることを確認するために、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> を使用して `ModelState` エラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="edb23-353">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="edb23-354">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="edb23-354">Additional resources</span></span>

* [<span data-ttu-id="edb23-355">dotnet テストと xUnit を使用した .NET Core での単体テスト C#</span><span class="sxs-lookup"><span data-stu-id="edb23-355">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="edb23-356">[コードの単体テスト](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="edb23-356">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="edb23-357">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="edb23-357">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="edb23-358">Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築</span><span class="sxs-lookup"><span data-stu-id="edb23-358">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="edb23-359">XUnit.net の概要:.NET SDK コマンド ラインでの .Net Core の使用</span><span class="sxs-lookup"><span data-stu-id="edb23-359">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="edb23-360">Moq</span><span class="sxs-lookup"><span data-stu-id="edb23-360">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="edb23-361">Moq クイック スタート</span><span class="sxs-lookup"><span data-stu-id="edb23-361">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)
* <span data-ttu-id="edb23-362">[JustMockLite](https://github.com/telerik/JustMockLite):.NET 開発者向けのモック フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="edb23-362">[JustMockLite](https://github.com/telerik/JustMockLite): A mocking framework for .NET developers.</span></span> <span data-ttu-id="edb23-363">("*Microsoft では保守管理もサポートも行っていません。* ")</span><span class="sxs-lookup"><span data-stu-id="edb23-363">(*Not maintained or supported by Microsoft.*)</span></span>

::: moniker-end
