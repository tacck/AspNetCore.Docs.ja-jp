---
title: ASP.NET Core での Razor ページの概要
author: Rick-Anderson
description: ASP.NET Core の Razor Pages を使用して、ページのコーディングに重点を置いたシナリオをより簡略化して、MVC を使用する場合よりも生産性を高める方法について説明します。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 203d77a2caaba9156419a04754fe162dd57052c1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012710"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="6163a-103">ASP.NET Core での Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="6163a-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6163a-104">[Rick Anderson](https://twitter.com/RickAndMSFT) および [Ryan Nowak](https://github.com/rynowak) 著</span><span class="sxs-lookup"><span data-stu-id="6163a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="6163a-105">Razor ページを利用することで、ページのコーディングに今まで以上に集中できます。また、コントローラーとビューを使用する場合より生産的になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="6163a-106">モデル ビュー コントローラーのアプローチを使用するチュートリアルをお探しの場合は、「[Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc)」 (ASP.NET Core MVC の概要) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="6163a-107">このドキュメントでは、Razor ページの概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="6163a-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="6163a-108">手順を追って説明するチュートリアルではありません。</span><span class="sxs-lookup"><span data-stu-id="6163a-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="6163a-109">セクションの一部を理解できない場合は、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="6163a-110">ASP.NET Core の概要については、「[ASP.NET Core の概要](xref:index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6163a-111">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6163a-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6163a-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6163a-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6163a-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6163a-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6163a-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6163a-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="6163a-115">Razor ページ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="6163a-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6163a-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6163a-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6163a-117">Razor ページ プロジェクトを作成する詳細な手順については、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6163a-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6163a-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6163a-119">コマンド ラインから `dotnet new webapp` を実行します。</span><span class="sxs-lookup"><span data-stu-id="6163a-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6163a-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6163a-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6163a-121">Razor ページ プロジェクトを作成する詳細な手順については、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="6163a-122">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="6163a-122">Razor Pages</span></span>

<span data-ttu-id="6163a-123">Razor ページは "*Startup.cs*" で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="6163a-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="6163a-124">基本ページを検討します。<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="6163a-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="6163a-125">上記のコードは、コントローラーとビューを含んだ ASP.NET Core アプリで使われる [Razor ビュー ファイル](xref:tutorials/first-mvc-app/adding-view)によく似ています。</span><span class="sxs-lookup"><span data-stu-id="6163a-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="6163a-126">違いは [`@page`](xref:mvc/views/razor#page) ディレクティブにあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="6163a-127">`@page` はファイルを MVC アクションにします。つまり、コントローラーを経由せずに要求を直接処理します。</span><span class="sxs-lookup"><span data-stu-id="6163a-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="6163a-128">`@page` はページ上で最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="6163a-129">`@page` はその他の [Razor](xref:mvc/views/razor) コンストラクトの動作に影響します。</span><span class="sxs-lookup"><span data-stu-id="6163a-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="6163a-130">Razor ページのファイル名には " *.cshtml*" サフィックスが付きます。</span><span class="sxs-lookup"><span data-stu-id="6163a-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="6163a-131">`PageModel` クラスを使用している類似したページが、次の 2 つのファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="6163a-132">*Pages/Index2.cshtml* ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="6163a-133">*Pages/Index2.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="6163a-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="6163a-134">規則により、`PageModel` クラス ファイルは、Razor ページ ファイルと同じ名前に " *.cs*" が付加された名前になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="6163a-135">たとえば、上の Razor ページは "*Pages/Index2.cshtml*" になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="6163a-136">`PageModel` クラスを含むファイル名は、*Pages/Index2.cshtml.cs* になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="6163a-137">URL パスのページへの関連付けは、ファイル システム内のページの場所によって決定されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="6163a-138">次の表に、Razor ページ パスと一致 URL を示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="6163a-139">ファイル名とパス</span><span class="sxs-lookup"><span data-stu-id="6163a-139">File name and path</span></span>               | <span data-ttu-id="6163a-140">一致 URL</span><span class="sxs-lookup"><span data-stu-id="6163a-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="6163a-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="6163a-142">`/` または `/Index`</span><span class="sxs-lookup"><span data-stu-id="6163a-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="6163a-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="6163a-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="6163a-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="6163a-146">`/Store` または `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="6163a-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="6163a-147">メモ:</span><span class="sxs-lookup"><span data-stu-id="6163a-147">Notes:</span></span>

* <span data-ttu-id="6163a-148">既定では、ランタイムが "*Pages*" フォルダー内で Razor ページ ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="6163a-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="6163a-149">`Index` は、URL にページが含まれない場合の既定のページになります。</span><span class="sxs-lookup"><span data-stu-id="6163a-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="6163a-150">基本フォームを作成する</span><span class="sxs-lookup"><span data-stu-id="6163a-150">Write a basic form</span></span>

<span data-ttu-id="6163a-151">Razor ページは、アプリの構築時に Web ブラウザーで使用される一般的なパターンを実装しやすくするために設計されています。</span><span class="sxs-lookup"><span data-stu-id="6163a-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="6163a-152">[モデル バインド](xref:mvc/models/model-binding)、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、および HTML ヘルパーはすべて、Razor ページ クラスで定義されたプロパティで "*機能します*"。</span><span class="sxs-lookup"><span data-stu-id="6163a-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="6163a-153">`Contact` モデルの基本的な "お問い合わせ" フォームを実装するページを考察します。</span><span class="sxs-lookup"><span data-stu-id="6163a-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="6163a-154">このドキュメントのサンプルでは、[Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) ファイルで `DbContext` が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="6163a-155">インメモリ データベースには、`Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="6163a-155">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="6163a-156">データ モデル:</span><span class="sxs-lookup"><span data-stu-id="6163a-156">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="6163a-157">db コンテキスト:</span><span class="sxs-lookup"><span data-stu-id="6163a-157">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="6163a-158">*Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="6163a-159">*Pages/Create.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="6163a-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="6163a-160">規則により、`PageModel` クラスは `<PageName>Model` と呼ばれ、ページと同じ名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="6163a-161">`PageModel` クラスでは、ページの表示からロジックを分離できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="6163a-162">これは、ページに送信される要求のページ ハンドラーと、ページのレンダリングに使用されるデータを定義します。</span><span class="sxs-lookup"><span data-stu-id="6163a-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="6163a-163">この分離により可能になること:</span><span class="sxs-lookup"><span data-stu-id="6163a-163">This separation allows:</span></span>

* <span data-ttu-id="6163a-164">[依存関係の挿入](xref:fundamentals/dependency-injection)によるページの依存関係の管理。</span><span class="sxs-lookup"><span data-stu-id="6163a-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="6163a-165">単体テスト</span><span class="sxs-lookup"><span data-stu-id="6163a-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="6163a-166">このページには、(ユーザーがフォームを投稿したときに) `POST` 要求で実行される `OnPostAsync` "*ハンドラー メソッド*" があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="6163a-167">任意の HTTP 動詞のハンドラー メソッドを追加できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="6163a-168">最も一般的なハンドラーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6163a-168">The most common handlers are:</span></span>

* <span data-ttu-id="6163a-169">ページに必要な状態を初期化するための `OnGet`。</span><span class="sxs-lookup"><span data-stu-id="6163a-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="6163a-170">前のコードでは、`OnGet` メソッドにより "*CreateModel.cshtml*" Razor ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="6163a-171">フォームの送信を処理するための `OnPost`。</span><span class="sxs-lookup"><span data-stu-id="6163a-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="6163a-172">`Async` 名前付けサフィックスは省略可能ですが、非同期関数の規則でよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="6163a-173">上記のコードは、Razor ページでは一般的です。</span><span class="sxs-lookup"><span data-stu-id="6163a-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="6163a-174">コントローラーとビューを利用する ASP.NET アプリに慣れている場合:</span><span class="sxs-lookup"><span data-stu-id="6163a-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="6163a-175">前の例の `OnPostAsync` コードは、一般的なコントローラー コードに似ています。</span><span class="sxs-lookup"><span data-stu-id="6163a-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="6163a-176">[モデル バインド](xref:mvc/models/model-binding)、[検証](xref:mvc/models/validation)、アクションの結果など、MVC プリミティブのほとんどは Controllers ページや Razor ページと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="6163a-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="6163a-177">上記の `OnPostAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="6163a-177">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="6163a-178">`OnPostAsync` の基本的な流れは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6163a-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="6163a-179">検証エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="6163a-179">Check for validation errors.</span></span>

* <span data-ttu-id="6163a-180">エラーがない場合は、データを保存し、リダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="6163a-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="6163a-181">エラーがある場合は、検証メッセージとともにページをもう一度表示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="6163a-182">多くの場合、検証エラーはクライアントで検出され、サーバーには送信されません。</span><span class="sxs-lookup"><span data-stu-id="6163a-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="6163a-183">*Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="6163a-184">*Pages/Create.cshtml* からレンダリングされた HTML:</span><span class="sxs-lookup"><span data-stu-id="6163a-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="6163a-185">前のコードで投稿したフォーム:</span><span class="sxs-lookup"><span data-stu-id="6163a-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="6163a-186">有効なデータ:</span><span class="sxs-lookup"><span data-stu-id="6163a-186">With valid data:</span></span>

  * <span data-ttu-id="6163a-187">`OnPostAsync` ハンドラー メソッドにより <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> ヘルパー メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="6163a-188">`RedirectToPage` は <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="6163a-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="6163a-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="6163a-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="6163a-190">はアクションの結果です。</span><span class="sxs-lookup"><span data-stu-id="6163a-190">Is an action result.</span></span>
    * <span data-ttu-id="6163a-191">は、(コントローラーやビューで使用される) `RedirectToAction` や`RedirectToRoute` に似ています。</span><span class="sxs-lookup"><span data-stu-id="6163a-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="6163a-192">はページ用にカスタマイズされています。</span><span class="sxs-lookup"><span data-stu-id="6163a-192">Is customized for pages.</span></span> <span data-ttu-id="6163a-193">上記のサンプルでは、ルート インデックス ページ (`/Index`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="6163a-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="6163a-194">`RedirectToPage` については、「[ページの URL の生成](#url_gen)」セクションで詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="6163a-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="6163a-195">サーバーに検証エラーが渡される:</span><span class="sxs-lookup"><span data-stu-id="6163a-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="6163a-196">`OnPostAsync` ハンドラー メソッドにより <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> ヘルパー メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="6163a-197">`Page` は <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="6163a-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="6163a-198">`Page` を返すのは、コントローラーのアクションが `View` を返す方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="6163a-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="6163a-199">`PageResult` はハンドラー メソッドの既定の戻り値の型です。</span><span class="sxs-lookup"><span data-stu-id="6163a-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="6163a-200">`void` を返すハンドラー メソッドがページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="6163a-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="6163a-201">前の例では、値のないフォームが投稿された結果、[ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) から false が返されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="6163a-202">このサンプルでは、検証エラーはクライアントに表示されません。</span><span class="sxs-lookup"><span data-stu-id="6163a-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="6163a-203">検証エラーの処理については、このドキュメントの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="6163a-203">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="6163a-204">クライアント側の検証で検証エラーが検出される:</span><span class="sxs-lookup"><span data-stu-id="6163a-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="6163a-205">データはサーバーに投稿されて**いません**。</span><span class="sxs-lookup"><span data-stu-id="6163a-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="6163a-206">クライアント側の検証については、このドキュメントの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="6163a-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="6163a-207">`Customer` プロパティは [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性を使用してモデル バインドにオプトインします。</span><span class="sxs-lookup"><span data-stu-id="6163a-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="6163a-208">`[BindProperty]` は、クライアントが変更するべきではないプロパティを含むモデルで使用**しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="6163a-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="6163a-209">詳細については、「[過剰ポスティング](xref:data/ef-rp/crud#overposting)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6163a-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="6163a-210">既定では、Razor ページはプロパティを非 `GET` 動詞とのみバインドします。</span><span class="sxs-lookup"><span data-stu-id="6163a-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="6163a-211">プロパティにバインドすると、HTTP データをモデル型に変換する目的でコードを記述する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="6163a-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="6163a-212">同じプロパティを使用してバインドすることでコードを減らし、フィールド (`<input asp-for="Customer.Name">`) からレンダリングして入力を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="6163a-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="6163a-213">*Pages/Create.cshtml* ビュー ファイルのレビュー:</span><span class="sxs-lookup"><span data-stu-id="6163a-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="6163a-214">前のコードでは、[入力タグ ヘルパー](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` によって HTML `<input>` 要素が `Customer.Name` モデル式にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="6163a-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="6163a-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) でタグ ヘルパーを使用可能にします。</span><span class="sxs-lookup"><span data-stu-id="6163a-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="6163a-216">ホーム ページ</span><span class="sxs-lookup"><span data-stu-id="6163a-216">The home page</span></span>

<span data-ttu-id="6163a-217">*Index.cshtml* はホーム ページです。</span><span class="sxs-lookup"><span data-stu-id="6163a-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="6163a-218">関連付けられた `PageModel` クラス (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="6163a-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="6163a-219">*Index.cshtml* ファイルには、次のマークアップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="6163a-220">`<a /a>` [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)では、`asp-route-{value}` 属性を使用して編集ページへのリンクが生成されました。</span><span class="sxs-lookup"><span data-stu-id="6163a-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="6163a-221">リンクには、連絡先 ID とともにルート データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="6163a-222">たとえば、`https://localhost:5001/Edit/1` のようにします。</span><span class="sxs-lookup"><span data-stu-id="6163a-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="6163a-223">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="6163a-224">*Index.cshtml* ファイルには、各顧客の連絡先の削除ボタンを作成するマークアップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="6163a-225">レンダリングされた HTML:</span><span class="sxs-lookup"><span data-stu-id="6163a-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="6163a-226">HTML で削除ボタンがレンダリングされる場合、その [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) には次のパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="6163a-227">`asp-route-id` 属性によって指定された顧客の連絡先 ID。</span><span class="sxs-lookup"><span data-stu-id="6163a-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="6163a-228">`asp-page-handler` 属性によって指定された `handler`。</span><span class="sxs-lookup"><span data-stu-id="6163a-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="6163a-229">ボタンが選択されると、フォームの `POST` 要求がサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="6163a-230">慣例により、ハンドラー メソッドの名前はスキーム `OnPost[handler]Async` に従った `handler` パラメーターの値に基づいて選択されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="6163a-231">この例では `handler` が `delete` であるため、`OnPostDeleteAsync` ハンドラー メソッドを使用して `POST` 要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="6163a-232">`asp-page-handler` が `remove` などの別の値に設定されている場合、名前が `OnPostRemoveAsync` のハンドラー メソッドが選択されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="6163a-233">`OnPostDeleteAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="6163a-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="6163a-234">クエリ文字列から `id` を取得します。</span><span class="sxs-lookup"><span data-stu-id="6163a-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="6163a-235">`FindAsync` を使用してデータベースから顧客の連絡先を照会します。</span><span class="sxs-lookup"><span data-stu-id="6163a-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="6163a-236">顧客の連絡先が見つからない場合、それは削除されており、データベースが更新されています。</span><span class="sxs-lookup"><span data-stu-id="6163a-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="6163a-237">ルート インデックス ページ (`/Index`) にリダイレクトされるように、<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6163a-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="6163a-238">Edit.cshtml ファイル</span><span class="sxs-lookup"><span data-stu-id="6163a-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="6163a-239">最初の行には `@page "{id:int}"` ディレクティブが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="6163a-240">ルーティングの制約 `"{id:int}"` は、`int` ルート データを含むページへの要求を受け入れるようにページに指示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="6163a-241">ページへの要求に `int` に変換できるルート データが含まれていない場合は、ランタイムで HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6163a-242">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6163a-243">*Edit.cshtml.cs* ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-243">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="6163a-244">検証</span><span class="sxs-lookup"><span data-stu-id="6163a-244">Validation</span></span>

<span data-ttu-id="6163a-245">検証規則:</span><span class="sxs-lookup"><span data-stu-id="6163a-245">Validation rules:</span></span>

* <span data-ttu-id="6163a-246">はモデル クラスで指定 (宣言) されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="6163a-247">はアプリ内のあらゆる場所で適用されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="6163a-248"><xref:System.ComponentModel.DataAnnotations> 名前空間には、クラスまたはプロパティに宣言的に適用される一連の組み込みの検証属性があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="6163a-249">また、DataAnnotations には、書式設定を支援し、どの検証を行わない [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) のような書式設定属性もあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="6163a-250">`Customer` モデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="6163a-250">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="6163a-251">次の *Create.cshtml* ビュー ファイルを使用:</span><span class="sxs-lookup"><span data-stu-id="6163a-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="6163a-252">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="6163a-252">The preceding code:</span></span>

* <span data-ttu-id="6163a-253">jQuery と jQuery 検証スクリプトが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6163a-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="6163a-254">`<div />` と `<span />` [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使用して次を有効にします。</span><span class="sxs-lookup"><span data-stu-id="6163a-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="6163a-255">クライアント側の検証。</span><span class="sxs-lookup"><span data-stu-id="6163a-255">Client-side validation.</span></span>
  * <span data-ttu-id="6163a-256">検証エラー レンダリング。</span><span class="sxs-lookup"><span data-stu-id="6163a-256">Validation error rendering.</span></span>

* <span data-ttu-id="6163a-257">次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="6163a-258">名前値なしで Create フォームを投稿すると、このフォームに "The Name field is required." (名前フィールドは必須です。) というエラー メッセージ</span><span class="sxs-lookup"><span data-stu-id="6163a-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="6163a-259">が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-259">on the form.</span></span> <span data-ttu-id="6163a-260">JavaScript がクライアントで有効になっている場合、サーバーに投稿されず、エラーがブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="6163a-261">`[StringLength(10)]` 属性によって、レンダリングされた HTML で `data-val-length-max="10"` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="6163a-262">`data-val-length-max` により、指定の最大長を超える入力がブラウザーで禁止されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="6163a-263">[Fiddler](https://www.telerik.com/fiddler) のようなツールを投稿の編集と返信に使用する場合:</span><span class="sxs-lookup"><span data-stu-id="6163a-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="6163a-264">名前の長さ値が 10 を超えています。</span><span class="sxs-lookup"><span data-stu-id="6163a-264">With the name longer than 10.</span></span>
* <span data-ttu-id="6163a-265">"The field Name must be a string with a maximum length of 10." (名前フィールドは最大長が 10 の文字列になります。) というエラー メッセージが</span><span class="sxs-lookup"><span data-stu-id="6163a-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="6163a-266">返されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-266">is returned.</span></span>

<span data-ttu-id="6163a-267">次の `Movie` モデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="6163a-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="6163a-268">検証属性では、適用対象のモデル プロパティに適用する動作が指定されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="6163a-269">`Required` および `MinimumLength` 属性は、プロパティに値が必要であることを示します。ただし、この検証を満たすためにユーザーが空白を入力することは禁止されていません。</span><span class="sxs-lookup"><span data-stu-id="6163a-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="6163a-270">`RegularExpression` 属性は、入力できる文字を制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="6163a-271">上のコード "Genre" では:</span><span class="sxs-lookup"><span data-stu-id="6163a-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="6163a-272">文字のみを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-272">Must only use letters.</span></span>
  * <span data-ttu-id="6163a-273">最初の文字は大文字にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="6163a-274">空白、数字、特殊文字は使用できません。</span><span class="sxs-lookup"><span data-stu-id="6163a-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="6163a-275">`RegularExpression` "評価":</span><span class="sxs-lookup"><span data-stu-id="6163a-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="6163a-276">最初の文字が大文字である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="6163a-277">後続のスペースでは、特殊文字と数字が使用できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="6163a-278">"PG-13" は評価に対して有効ですが、"Genre" に対しては失敗します。</span><span class="sxs-lookup"><span data-stu-id="6163a-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="6163a-279">`Range` 属性は、指定した範囲内に値を制限します。</span><span class="sxs-lookup"><span data-stu-id="6163a-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="6163a-280">`StringLength` 属性により、文字列プロパティの最大長が設定されます。任意で最小長も設定できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="6163a-281">値の型 (`decimal`、`int`、`float`、`DateTime` など) は本質的に必須ではなく、`[Required]` 属性を必要としません。</span><span class="sxs-lookup"><span data-stu-id="6163a-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="6163a-282">`Movie` モデルの [作成] ページには、エラーと無効な値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![複数 jQuery クライアント側検証エラーが表示されたムービー ビュー フォーム](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="6163a-284">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="6163a-284">For more information, see:</span></span>

* [<span data-ttu-id="6163a-285">Movie アプリに検証を追加する</span><span class="sxs-lookup"><span data-stu-id="6163a-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="6163a-286">[ASP.NET Core のモデル検証](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="6163a-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="6163a-287">OnGet ハンドラー フォールバックを使用した HEAD 要求の処理</span><span class="sxs-lookup"><span data-stu-id="6163a-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="6163a-288">`HEAD` 要求により、特定のリソースのヘッダーを取得できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="6163a-289">`GET` 要求とは異なり、`HEAD` 要求から応答本文は返されません。</span><span class="sxs-lookup"><span data-stu-id="6163a-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="6163a-290">通常、`HEAD` 要求に対して `OnHead` ハンドラーが作成され、呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="6163a-291">`OnHead` ハンドラーが定義されていない場合、Razor ページは `OnGet` ハンドラーの呼び出しにフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="6163a-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="6163a-292">XSRF/CSRF および Razor ページ</span><span class="sxs-lookup"><span data-stu-id="6163a-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="6163a-293">Razor ページは、[偽造防止検証](xref:security/anti-request-forgery)によって保護されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="6163a-294">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) により HTML フォーム要素に偽造防止トークンが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="6163a-295">Razor ページでのレイアウト、パーシャル、テンプレート、およびタグ ヘルパーの使用</span><span class="sxs-lookup"><span data-stu-id="6163a-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="6163a-296">ページは、Razor ビュー エンジンのすべての機能で動作します。</span><span class="sxs-lookup"><span data-stu-id="6163a-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="6163a-297">レイアウト、パーシャル、テンプレート、タグ ヘルパー、" *_ViewStart.cshtml*"、" *_ViewImports.cshtml*" は、従来の Razor ビューの場合と同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="6163a-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="6163a-298">これらの機能の一部を利用してこのページをまとめてみましょう。</span><span class="sxs-lookup"><span data-stu-id="6163a-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="6163a-299">[レイアウト ページ](xref:mvc/views/layout)を *Pages/Shared/_Layout.cshtml* に追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="6163a-300">[レイアウト](xref:mvc/views/layout)は次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="6163a-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="6163a-301">(ページでレイアウトを止めない限り) 各ページのレイアウトを制御します。</span><span class="sxs-lookup"><span data-stu-id="6163a-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="6163a-302">JavaScript やスタイルシートなどの HTML 構造をインポートします。</span><span class="sxs-lookup"><span data-stu-id="6163a-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="6163a-303">`@RenderBody()` が呼び出されるところで Razor ページの内容が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="6163a-304">詳細については、[レイアウトに関するページ](xref:mvc/views/layout)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="6163a-305">[Layout](xref:mvc/views/layout#specifying-a-layout) プロパティは *Pages/_ViewStart.cshtml* で設定されています。</span><span class="sxs-lookup"><span data-stu-id="6163a-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="6163a-306">レイアウトは、*Pages/Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="6163a-307">ページは現在のページと同じフォルダーから開始して、階層的に他のビュー (レイアウト、テンプレート、パーシャル) を検索します。</span><span class="sxs-lookup"><span data-stu-id="6163a-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="6163a-308">"*Pages/Shared*" フォルダー内のレイアウトは、"*Pages*" フォルダー配下の任意の Razor ページから使用できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="6163a-309">レイアウト ファイルは *Pages/Shared* フォルダーに入ります。</span><span class="sxs-lookup"><span data-stu-id="6163a-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="6163a-310">レイアウト ファイルを *Views/Shared* フォルダー内に配置**しない**ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6163a-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="6163a-311">*Views/Shared* は MVC ビュー パターンです。</span><span class="sxs-lookup"><span data-stu-id="6163a-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="6163a-312">Razor ページは、パス規則ではなく、フォルダー階層に依存することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="6163a-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="6163a-313">Razor ページからのビュー検索には、"*Pages*" フォルダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6163a-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="6163a-314">MVC コントローラーで使用されているレイアウト、テンプレート、パーシャルと、従来の Razor ビューは "*機能します*"。</span><span class="sxs-lookup"><span data-stu-id="6163a-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="6163a-315">*Pages/_ViewImports.cshtml* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="6163a-316">`@namespace` はこのチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="6163a-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="6163a-317">`@addTagHelper` ディレクティブにより、[組み込みタグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/Index)が *Pages* フォルダー内のすべてのページにもたらされます。</span><span class="sxs-lookup"><span data-stu-id="6163a-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="6163a-318">ページに設定される `@namespace` ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="6163a-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="6163a-319">`@namespace` ディレクティブは、ページの名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="6163a-320">`@model` ディレクティブには、名前空間を含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6163a-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="6163a-321">`@namespace` ディレクティブが *_ViewImports.cshtml* に含まれていると、指定した名前空間が `@namespace` ディレクティブをインポートするページで生成された名前空間のプレフィックスを提供します。</span><span class="sxs-lookup"><span data-stu-id="6163a-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="6163a-322">生成された名前空間の残りの部分 (サフィックスの部分) は、 *_ViewImports.cshtml* を含むフォルダーとページを含むフォルダー間のドットで区切られた相対パスです。</span><span class="sxs-lookup"><span data-stu-id="6163a-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="6163a-323">たとえば、`PageModel` クラス *Pages/Customers/Edit.cshtml.cs* は名前空間を明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="6163a-324">*Pages/_ViewImports.cshtml* ファイルは次の名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="6163a-325">"*Pages/Customers/Edit.cshtml*" Razor ページの生成された名前空間は、`PageModel` クラスと同じです。</span><span class="sxs-lookup"><span data-stu-id="6163a-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="6163a-326">`@namespace` は "*従来の Razor ビューでも機能します。* "</span><span class="sxs-lookup"><span data-stu-id="6163a-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="6163a-327">*Pages/Create.cshtml* ビュー ファイル を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="6163a-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="6163a-328">更新後の *Pages/Create.cshtml* ビュー ファイル、 *_ViewImports.cshtml*、前のレイアウト ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="6163a-329">前のコードでは、 *_ViewImports.cshtml* によって名前空間とタグ ヘルパーがインポートされました。</span><span class="sxs-lookup"><span data-stu-id="6163a-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="6163a-330">レイアウト ファイルによって JavaScript ファイルがインポートされました。</span><span class="sxs-lookup"><span data-stu-id="6163a-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="6163a-331">[Razor ページのスタート プロジェクト](#rpvs17)には、クライアント側の検証をフックする "*Pages/_ValidationScriptsPartial.cshtml*" が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="6163a-332">部分ビューの詳細については、「<xref:mvc/views/partial>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="6163a-333">ページの URL の生成</span><span class="sxs-lookup"><span data-stu-id="6163a-333">URL generation for Pages</span></span>

<span data-ttu-id="6163a-334">上に示した `Create` ページでは、`RedirectToPage` を使用します。</span><span class="sxs-lookup"><span data-stu-id="6163a-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="6163a-335">アプリには次のファイル/フォルダー構造があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="6163a-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="6163a-336">*/Pages*</span></span>

  * <span data-ttu-id="6163a-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="6163a-338">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="6163a-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="6163a-339">*/Customers*</span></span>

    * <span data-ttu-id="6163a-340">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="6163a-341">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="6163a-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-342">*Index.cshtml*</span></span>

<span data-ttu-id="6163a-343">成功すると、*Pages/Customers/Create.cshtml* ページと *Pages/Customers/Edit.cshtml* ページが *Pages/Customers/Index.cshtml* にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="6163a-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="6163a-344">文字列 `./Index` は、前のページにアクセスするために使用される相対ページ名です。</span><span class="sxs-lookup"><span data-stu-id="6163a-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="6163a-345">これは、*Pages/Customers/Index.cshtml* ページへの URI を生成するために使われます。</span><span class="sxs-lookup"><span data-stu-id="6163a-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="6163a-346">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="6163a-347">絶対ページ名 `/Index` は、*Pages/Index.cshtml* ページへの URL を生成するために使われます。</span><span class="sxs-lookup"><span data-stu-id="6163a-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="6163a-348">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="6163a-349">ページ名は、先頭の `/` を含む、ルート */Pages* フォルダーからページへのパスです (たとえば `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="6163a-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="6163a-350">先述の URL 生成サンプルでは、URL のハードコーディングに関する拡張オプションと機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="6163a-351">URL の生成は[ルーティング](xref:mvc/controllers/routing)を使用し、ターゲット パスで定義されたルート方法に従って、パラメーターの生成とエンコードができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="6163a-352">ページの URL 生成は、相対名をサポートします。</span><span class="sxs-lookup"><span data-stu-id="6163a-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="6163a-353">次の表に、*Pages/Customers/Create.cshtml* の異なる `RedirectToPage` パラメーターで選択されたインデックス ページを示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="6163a-354">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="6163a-354">RedirectToPage(x)</span></span>| <span data-ttu-id="6163a-355">ページ</span><span class="sxs-lookup"><span data-stu-id="6163a-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="6163a-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="6163a-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="6163a-357">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="6163a-357">*Pages/Index*</span></span> |
| <span data-ttu-id="6163a-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="6163a-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="6163a-359">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="6163a-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="6163a-360">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="6163a-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="6163a-361">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="6163a-361">*Pages/Index*</span></span> |
| <span data-ttu-id="6163a-362">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="6163a-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="6163a-363">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="6163a-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="6163a-364">`RedirectToPage("Index")`、`RedirectToPage("./Index")`、`RedirectToPage("../Index")` は*相対名*です。</span><span class="sxs-lookup"><span data-stu-id="6163a-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="6163a-365">`RedirectToPage` パラメーターは現在のページのパスと*組み合わされて*、ターゲット ページの名前を計算します。</span><span class="sxs-lookup"><span data-stu-id="6163a-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="6163a-366">相対名のリンクは、複雑な構造を持つサイトを構築する際に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6163a-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="6163a-367">あるフォルダー内のページ間をリンクする目的で相対名を使用するとき:</span><span class="sxs-lookup"><span data-stu-id="6163a-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="6163a-368">フォルダー名を変更しても相対リンクは壊れません。</span><span class="sxs-lookup"><span data-stu-id="6163a-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="6163a-369">フォルダー名が含まれていないため、リンクは壊れません。</span><span class="sxs-lookup"><span data-stu-id="6163a-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="6163a-370">別の [[区分]](xref:mvc/controllers/areas) のページにリダイレクトするには、その区分を指定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="6163a-371">詳細については、次のトピックを参照してください。 <xref:mvc/controllers/areas> および <xref:razor-pages/razor-pages-conventions></span><span class="sxs-lookup"><span data-stu-id="6163a-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="6163a-372">ViewData 属性</span><span class="sxs-lookup"><span data-stu-id="6163a-372">ViewData attribute</span></span>

<span data-ttu-id="6163a-373">データは <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> を含むページに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="6163a-374">`[ViewData]` 属性を含むプロパティにはその値が格納され、<xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6163a-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="6163a-375">次の例では、`AboutModel` により、`[ViewData]` 属性が `Title` プロパティに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="6163a-376">[About] ページでは、モデル プロパティとして `Title` プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="6163a-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="6163a-377">レイアウトでは、タイトルは ViewData ディクショナリから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6163a-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="6163a-378">TempData</span><span class="sxs-lookup"><span data-stu-id="6163a-378">TempData</span></span>

<span data-ttu-id="6163a-379">ASP.NET Core により <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> が公開されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="6163a-380">このプロパティは、読み取られるまでデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="6163a-380">This property stores data until it's read.</span></span> <span data-ttu-id="6163a-381"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> メソッドと <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> メソッドは、削除せずにデータを確認するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="6163a-382">`TempData` は、複数の要求に対してデータが必要な場合のリダイレクトに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6163a-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="6163a-383">次のコードは、`TempData` を使用して `Message` の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="6163a-384">*Pages/Customers/Index.cshtml* ファイル内の次のマークアップは、`TempData` を使用して `Message` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="6163a-385">*Pages/Customers/Index.cshtml.cs* ページは、`[TempData]` 属性を `Message` プロパティに適用します。</span><span class="sxs-lookup"><span data-stu-id="6163a-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="6163a-386">詳細については、「[TempData](xref:fundamentals/app-state#tempdata)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="6163a-387">ページあたり複数のハンドラー</span><span class="sxs-lookup"><span data-stu-id="6163a-387">Multiple handlers per page</span></span>

<span data-ttu-id="6163a-388">次のページでは、`asp-page-handler` タグ ヘルパーを使用して 2 つのハンドラーにマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="6163a-389">前の例のフォームには、それぞれが `FormActionTagHelper` を使用して異なる URL に送信する 2 つの送信ボタンがあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="6163a-390">`asp-page-handler` 属性は、`asp-page` のコンパニオンです。</span><span class="sxs-lookup"><span data-stu-id="6163a-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="6163a-391">`asp-page-handler` はページごとに定義されている各ハンドラー メソッドに送信する URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="6163a-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="6163a-392">サンプルは現在のページにリンクしているため、`asp-page` は指定されません。</span><span class="sxs-lookup"><span data-stu-id="6163a-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="6163a-393">ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="6163a-393">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="6163a-394">上記のコードは、*名前付きハンドラー メソッド*を使用しています。</span><span class="sxs-lookup"><span data-stu-id="6163a-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="6163a-395">名前付きハンドラー メソッドは、名前の `On<HTTP Verb>` の後および `Async` の前 (ある場合) のテキストを取得して作成されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="6163a-396">前の例では、ページ メソッドは OnPost**JoinList**Async と OnPost**JoinListUC**Async です。</span><span class="sxs-lookup"><span data-stu-id="6163a-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="6163a-397">*OnPost* と *Async* を削除すると、ハンドラー名は `JoinList` と `JoinListUC` になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="6163a-398">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="6163a-399">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="6163a-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="6163a-400">カスタム ルート</span><span class="sxs-lookup"><span data-stu-id="6163a-400">Custom routes</span></span>

<span data-ttu-id="6163a-401">`@page` ディレクティブを次に使用します:</span><span class="sxs-lookup"><span data-stu-id="6163a-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="6163a-402">カスタム ルートをページに指定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-402">Specify a custom route to a page.</span></span> <span data-ttu-id="6163a-403">たとえば、[バージョン情報] ページへのルートを `@page "/Some/Other/Path"` を使用して `/Some/Other/Path` に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="6163a-404">ページの既定のルートにセグメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-404">Append segments to a page's default route.</span></span> <span data-ttu-id="6163a-405">たとえば、"item" セグメントを `@page "item"` を使用してページの既定のルートに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="6163a-406">ページの既定のルートにパラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="6163a-407">たとえば、`@page "{id}"` を含むページに ID パラメーター `id` を必須とすることができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="6163a-408">パスの先頭のチルダ (`~`) によって指定されたルートの相対パスがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6163a-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="6163a-409">たとえば、`@page "~/Some/Other/Path"` は `@page "/Some/Other/Path"` と同じです。</span><span class="sxs-lookup"><span data-stu-id="6163a-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="6163a-410">URL 内のクエリ文字列 `?handler=JoinList` が気に入らない場合は、ルートを変更して URL のパス部分にハンドラー名を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="6163a-411">`@page` ディレクティブの後に二重引用符で囲んだルート テンプレートを追加して、ルートをカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="6163a-412">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="6163a-413">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="6163a-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="6163a-414">`handler` の後の `?` は、ルート パラメーターが省略可能なことを意味します。</span><span class="sxs-lookup"><span data-stu-id="6163a-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="6163a-415">詳細な構成と設定</span><span class="sxs-lookup"><span data-stu-id="6163a-415">Advanced configuration and settings</span></span>

<span data-ttu-id="6163a-416">次のセクションの構成と設定はほとんどのアプリで必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6163a-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="6163a-417">高度なオプションを構成するには、<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> を構成する <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> オーバーロードを使用します。</span><span class="sxs-lookup"><span data-stu-id="6163a-417">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="6163a-418"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> を使用してページのルート ディレクトリを設定したり、ページのアプリケーション モデルの規則を追加したりできます。</span><span class="sxs-lookup"><span data-stu-id="6163a-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="6163a-419">規則の詳細については、「[Razor ページの承認規則](xref:security/authorization/razor-pages-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="6163a-420">ビューをプリコンパイルするには、[Razor ビューのコンパイル](xref:mvc/views/view-compilation)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6163a-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="6163a-421">Razor Pages をコンテンツのルートに指定する</span><span class="sxs-lookup"><span data-stu-id="6163a-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="6163a-422">Razor Pages のルートは既定で */Pages* ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="6163a-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="6163a-423"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> を追加して、Razor ページをアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) に置くように指定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="6163a-424">Razor Pages をカスタム ルート ディレクトリに指定する</span><span class="sxs-lookup"><span data-stu-id="6163a-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="6163a-425">(相対パスを指定して) Razor ページをアプリのカスタム ルート ディレクトリに置くように指定するには、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> を追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="6163a-426">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6163a-426">Additional resources</span></span>

* <span data-ttu-id="6163a-427">この概要に基づく、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するページをご覧ください</span><span class="sxs-lookup"><span data-stu-id="6163a-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="6163a-428">属性と Razor ページを承認する</span><span class="sxs-lookup"><span data-stu-id="6163a-428">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="6163a-429">サンプル コードのダウンロードまたは表示</span><span class="sxs-lookup"><span data-stu-id="6163a-429">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6163a-430">[Rick Anderson](https://twitter.com/RickAndMSFT) および [Ryan Nowak](https://github.com/rynowak) 著</span><span class="sxs-lookup"><span data-stu-id="6163a-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="6163a-431">Razor ページは、ページ コーディングに重点を置いたシナリオをより簡略化し、生産性を高める ASP.NET Core MVC の新たな側面です。</span><span class="sxs-lookup"><span data-stu-id="6163a-431">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="6163a-432">モデル ビュー コントローラーのアプローチを使用するチュートリアルをお探しの場合は、「[Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc)」 (ASP.NET Core MVC の概要) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-432">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="6163a-433">このドキュメントでは、Razor ページの概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="6163a-433">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="6163a-434">手順を追って説明するチュートリアルではありません。</span><span class="sxs-lookup"><span data-stu-id="6163a-434">It's not a step by step tutorial.</span></span> <span data-ttu-id="6163a-435">セクションの一部を理解できない場合は、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-435">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="6163a-436">ASP.NET Core の概要については、「[ASP.NET Core の概要](xref:index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-436">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6163a-437">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6163a-437">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6163a-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6163a-438">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6163a-439">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6163a-439">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6163a-440">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6163a-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="6163a-441">Razor ページ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="6163a-441">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6163a-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6163a-442">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6163a-443">Razor ページ プロジェクトを作成する詳細な手順については、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-443">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6163a-444">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6163a-444">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6163a-445">コマンド ラインから `dotnet new webapp` を実行します。</span><span class="sxs-lookup"><span data-stu-id="6163a-445">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="6163a-446">Visual Studio for Mac から生成された *.csproj* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="6163a-446">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6163a-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6163a-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6163a-448">コマンド ラインから `dotnet new webapp` を実行します。</span><span class="sxs-lookup"><span data-stu-id="6163a-448">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="6163a-449">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="6163a-449">Razor Pages</span></span>

<span data-ttu-id="6163a-450">Razor ページは "*Startup.cs*" で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="6163a-450">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="6163a-451">基本ページを検討します。<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="6163a-451">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="6163a-452">上記のコードは、コントローラーとビューを含んだ ASP.NET Core アプリで使われる [Razor ビュー ファイル](xref:tutorials/first-mvc-app/adding-view)によく似ています。</span><span class="sxs-lookup"><span data-stu-id="6163a-452">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="6163a-453">違いは、`@page` ディレクティブにあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-453">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="6163a-454">`@page` はファイルを MVC アクションにします。つまり、コントローラーを経由せずに要求を直接処理します。</span><span class="sxs-lookup"><span data-stu-id="6163a-454">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="6163a-455">`@page` はページ上で最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-455">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="6163a-456">`@page` はその他の Razor コンストラクトの動作に影響します。</span><span class="sxs-lookup"><span data-stu-id="6163a-456">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="6163a-457">`PageModel` クラスを使用している類似したページが、次の 2 つのファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-457">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="6163a-458">*Pages/Index2.cshtml* ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-458">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="6163a-459">*Pages/Index2.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="6163a-459">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="6163a-460">規則により、`PageModel` クラス ファイルは、Razor ページ ファイルと同じ名前に " *.cs*" が付加された名前になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-460">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="6163a-461">たとえば、上の Razor ページは "*Pages/Index2.cshtml*" になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-461">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="6163a-462">`PageModel` クラスを含むファイル名は、*Pages/Index2.cshtml.cs* になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-462">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="6163a-463">URL パスのページへの関連付けは、ファイル システム内のページの場所によって決定されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-463">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="6163a-464">次の表に、Razor ページ パスと一致 URL を示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-464">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="6163a-465">ファイル名とパス</span><span class="sxs-lookup"><span data-stu-id="6163a-465">File name and path</span></span>               | <span data-ttu-id="6163a-466">一致 URL</span><span class="sxs-lookup"><span data-stu-id="6163a-466">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="6163a-467">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-467">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="6163a-468">`/` または `/Index`</span><span class="sxs-lookup"><span data-stu-id="6163a-468">`/` or `/Index`</span></span> |
| <span data-ttu-id="6163a-469">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-469">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="6163a-470">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-470">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="6163a-471">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-471">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="6163a-472">`/Store` または `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="6163a-472">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="6163a-473">メモ:</span><span class="sxs-lookup"><span data-stu-id="6163a-473">Notes:</span></span>

* <span data-ttu-id="6163a-474">既定では、ランタイムが "*Pages*" フォルダー内で Razor ページ ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="6163a-474">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="6163a-475">`Index` は、URL にページが含まれない場合の既定のページになります。</span><span class="sxs-lookup"><span data-stu-id="6163a-475">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="6163a-476">基本フォームを作成する</span><span class="sxs-lookup"><span data-stu-id="6163a-476">Write a basic form</span></span>

<span data-ttu-id="6163a-477">Razor ページは、アプリの構築時に Web ブラウザーで使用される一般的なパターンを実装しやすくするために設計されています。</span><span class="sxs-lookup"><span data-stu-id="6163a-477">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="6163a-478">[モデル バインド](xref:mvc/models/model-binding)、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、および HTML ヘルパーはすべて、Razor ページ クラスで定義されたプロパティで "*機能します*"。</span><span class="sxs-lookup"><span data-stu-id="6163a-478">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="6163a-479">`Contact` モデルの基本的な "お問い合わせ" フォームを実装するページを考察します。</span><span class="sxs-lookup"><span data-stu-id="6163a-479">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="6163a-480">このドキュメントのサンプルでは、[Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) ファイルで `DbContext` が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-480">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="6163a-481">データ モデル:</span><span class="sxs-lookup"><span data-stu-id="6163a-481">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="6163a-482">db コンテキスト:</span><span class="sxs-lookup"><span data-stu-id="6163a-482">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="6163a-483">*Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-483">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="6163a-484">*Pages/Create.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="6163a-484">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="6163a-485">規則により、`PageModel` クラスは `<PageName>Model` と呼ばれ、ページと同じ名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-485">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="6163a-486">`PageModel` クラスでは、ページの表示からロジックを分離できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-486">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="6163a-487">これは、ページに送信される要求のページ ハンドラーと、ページのレンダリングに使用されるデータを定義します。</span><span class="sxs-lookup"><span data-stu-id="6163a-487">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="6163a-488">この分離により可能になること:</span><span class="sxs-lookup"><span data-stu-id="6163a-488">This separation allows:</span></span>

* <span data-ttu-id="6163a-489">[依存関係の挿入](xref:fundamentals/dependency-injection)によるページの依存関係の管理。</span><span class="sxs-lookup"><span data-stu-id="6163a-489">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="6163a-490">ページを[単体テスト](xref:test/razor-pages-tests)します。</span><span class="sxs-lookup"><span data-stu-id="6163a-490">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="6163a-491">このページには、(ユーザーがフォームを投稿したときに) `POST` 要求で実行される `OnPostAsync` "*ハンドラー メソッド*" があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-491">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="6163a-492">任意の HTTP 動詞のハンドラー メソッドを追加できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-492">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="6163a-493">最も一般的なハンドラーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6163a-493">The most common handlers are:</span></span>

* <span data-ttu-id="6163a-494">ページに必要な状態を初期化するための `OnGet`。</span><span class="sxs-lookup"><span data-stu-id="6163a-494">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="6163a-495">[OnGet](#OnGet) サンプル。</span><span class="sxs-lookup"><span data-stu-id="6163a-495">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="6163a-496">フォームの送信を処理するための `OnPost`。</span><span class="sxs-lookup"><span data-stu-id="6163a-496">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="6163a-497">`Async` 名前付けサフィックスは省略可能ですが、非同期関数の規則でよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-497">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="6163a-498">上記のコードは、Razor ページでは一般的です。</span><span class="sxs-lookup"><span data-stu-id="6163a-498">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="6163a-499">コントローラーとビューを利用する ASP.NET アプリに慣れている場合:</span><span class="sxs-lookup"><span data-stu-id="6163a-499">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="6163a-500">前の例の `OnPostAsync` コードは、一般的なコントローラー コードに似ています。</span><span class="sxs-lookup"><span data-stu-id="6163a-500">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="6163a-501">[モデル バインド](xref:mvc/models/model-binding)、[検証](xref:mvc/models/validation)、[検証](xref:mvc/models/validation) アクションの結果などのほとんどの MVC プリミティブは共有されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-501">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="6163a-502">上記の `OnPostAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="6163a-502">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="6163a-503">`OnPostAsync` の基本的な流れは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6163a-503">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="6163a-504">検証エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="6163a-504">Check for validation errors.</span></span>

* <span data-ttu-id="6163a-505">エラーがない場合は、データを保存し、リダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="6163a-505">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="6163a-506">エラーがある場合は、検証メッセージとともにページをもう一度表示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-506">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="6163a-507">クライアント側の検証は、従来の ASP.NET Core MVC アプリケーションと同じです。</span><span class="sxs-lookup"><span data-stu-id="6163a-507">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="6163a-508">多くの場合、検証エラーはクライアントで検出され、サーバーには送信されません。</span><span class="sxs-lookup"><span data-stu-id="6163a-508">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="6163a-509">データが正常に入力されると、`OnPostAsync` ハンドラー メソッドが `RedirectToPage` ヘルパー メソッドを呼び出して `RedirectToPageResult` のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="6163a-509">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="6163a-510">`RedirectToPage` は、`RedirectToAction` や `RedirectToRoute` と同じような新しいアクション結果ですが、ページ用にカスタマイズされています。</span><span class="sxs-lookup"><span data-stu-id="6163a-510">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="6163a-511">上記のサンプルでは、ルート インデックス ページ (`/Index`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="6163a-511">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="6163a-512">`RedirectToPage` については、「[ページの URL の生成](#url_gen)」セクションで詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="6163a-512">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="6163a-513">送信されたフォームに検証エラー (サーバーに渡される) があると、`OnPostAsync` ハンドラー メソッドが `Page` ヘルパー メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6163a-513">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="6163a-514">`Page` は `PageResult` のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="6163a-514">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="6163a-515">`Page` を返すのは、コントローラーのアクションが `View` を返す方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="6163a-515">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="6163a-516">`PageResult` はハンドラー メソッドの既定の戻り値の型です。</span><span class="sxs-lookup"><span data-stu-id="6163a-516">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="6163a-517">`void` を返すハンドラー メソッドがページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="6163a-517">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="6163a-518">`Customer` プロパティは `[BindProperty]` 属性を使用してモデル バインドにオプトインします。</span><span class="sxs-lookup"><span data-stu-id="6163a-518">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="6163a-519">既定では、Razor ページはプロパティを非 `GET` 動詞とのみバインドします。</span><span class="sxs-lookup"><span data-stu-id="6163a-519">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="6163a-520">プロパティをバインドすることで、記述すべきコードの量を削減できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-520">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="6163a-521">同じプロパティを使用してバインドすることでコードを減らし、フィールド (`<input asp-for="Customer.Name">`) からレンダリングして入力を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="6163a-521">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="6163a-522">ホーム ページ (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="6163a-522">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="6163a-523">関連付けられた `PageModel` クラス (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="6163a-523">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="6163a-524">*Index.cshtml* ファイルには、各連絡先の編集リンクを作成するために次のマークアップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-524">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="6163a-525">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)では、`asp-route-{value}` 属性を使用して編集ページへのリンクが生成されました。</span><span class="sxs-lookup"><span data-stu-id="6163a-525">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="6163a-526">リンクには、連絡先 ID とともにルート データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-526">The link contains route data with the contact ID.</span></span> <span data-ttu-id="6163a-527">たとえば、`https://localhost:5001/Edit/1` のようにします。</span><span class="sxs-lookup"><span data-stu-id="6163a-527">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="6163a-528">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-528">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="6163a-529">タグ ヘルパーは `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` によって有効になります</span><span class="sxs-lookup"><span data-stu-id="6163a-529">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="6163a-530">*Pages/Edit.cshtml* ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-530">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="6163a-531">最初の行には `@page "{id:int}"` ディレクティブが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-531">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="6163a-532">ルーティングの制約 `"{id:int}"` は、`int` ルート データを含むページへの要求を受け入れるようにページに指示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-532">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="6163a-533">ページへの要求に `int` に変換できるルート データが含まれていない場合は、ランタイムで HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-533">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6163a-534">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-534">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6163a-535">*Pages/Edit.cshtml.cs* ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-535">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="6163a-536">*Index.cshtml* ファイルには、各顧客の連絡先の削除ボタンを作成するマークアップも含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-536">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="6163a-537">HTML で削除ボタンがレンダリングされる場合、その `formaction` には次のパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-537">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="6163a-538">`asp-route-id` 属性によって指定された顧客の連絡先 ID。</span><span class="sxs-lookup"><span data-stu-id="6163a-538">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="6163a-539">`asp-page-handler` 属性によって指定された `handler`。</span><span class="sxs-lookup"><span data-stu-id="6163a-539">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="6163a-540">顧客の連絡先 ID `1` でレンダリングされた削除ボタンの例を示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-540">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="6163a-541">ボタンが選択されると、フォームの `POST` 要求がサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-541">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="6163a-542">慣例により、ハンドラー メソッドの名前はスキーム `OnPost[handler]Async` に従った `handler` パラメーターの値に基づいて選択されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-542">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="6163a-543">この例では `handler` が `delete` であるため、`OnPostDeleteAsync` ハンドラー メソッドを使用して `POST` 要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-543">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="6163a-544">`asp-page-handler` が `remove` などの別の値に設定されている場合、名前が `OnPostRemoveAsync` のハンドラー メソッドが選択されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-544">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="6163a-545">次のコードは、`OnPostDeleteAsync` ハンドラーを示しています。</span><span class="sxs-lookup"><span data-stu-id="6163a-545">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="6163a-546">`OnPostDeleteAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="6163a-546">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="6163a-547">クエリ文字列から `id` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="6163a-547">Accepts the `id` from the query string.</span></span> <span data-ttu-id="6163a-548">*Index.cshtml* ページ ディレクティブにルーティング制約 `"{id:int?}"` が含まれていた場合、`id` はルート データから取得されることがあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-548">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="6163a-549">`id` のルート データは `https://localhost:5001/Customers/2` のように URI で指定されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-549">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="6163a-550">`FindAsync` を使用してデータベースから顧客の連絡先を照会します。</span><span class="sxs-lookup"><span data-stu-id="6163a-550">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="6163a-551">顧客の連絡先が見つかった場合、その連絡先は顧客の連絡先の一覧から削除されています。</span><span class="sxs-lookup"><span data-stu-id="6163a-551">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="6163a-552">データベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-552">The database is updated.</span></span>
* <span data-ttu-id="6163a-553">ルート インデックス ページ (`/Index`) にリダイレクトされるように、`RedirectToPage` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6163a-553">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="6163a-554">必要に応じてページのプロパティをマークする</span><span class="sxs-lookup"><span data-stu-id="6163a-554">Mark page properties as required</span></span>

<span data-ttu-id="6163a-555">`PageModel` 上のプロパティは、[必須](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)属性を使ってマークできます。</span><span class="sxs-lookup"><span data-stu-id="6163a-555">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="6163a-556">詳細については、[モデルの検証](xref:mvc/models/validation)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-556">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="6163a-557">OnGet ハンドラー フォールバックを使用した HEAD 要求の処理</span><span class="sxs-lookup"><span data-stu-id="6163a-557">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="6163a-558">`HEAD`HEAD 要求を使用すると、特定のリソースに対するヘッダーを取得できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-558">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="6163a-559">`GET` 要求とは異なり、`HEAD` 要求から応答本文は返されません。</span><span class="sxs-lookup"><span data-stu-id="6163a-559">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="6163a-560">通常、`HEAD` 要求に対して `OnHead` ハンドラーが作成され、呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-560">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="6163a-561">ASP.NET Core 2.1 以降では、`OnHead` ハンドラーが定義されていない場合、Razor ページは `OnGet` ハンドラーの呼び出しにフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="6163a-561">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="6163a-562">この動作は、`Startup.ConfigureServices` での [SetCompatibilityVersion](xref:mvc/compatibility-version) への呼び出しによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-562">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="6163a-563">既定のテンプレートでは、ASP.NET Core 2.1 および 2.2 で `SetCompatibilityVersion` の呼び出しが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-563">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="6163a-564">`SetCompatibilityVersion` は実質的に Razor ページのオプション `AllowMappingHeadRequestsToGetHandler` を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-564">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="6163a-565">`SetCompatibilityVersion` とのすべての動作にオプトインするのではなく、明示的に*特定の*動作にオプトインすることもできます。</span><span class="sxs-lookup"><span data-stu-id="6163a-565">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="6163a-566">次のコードでは、`OnGet` ハンドラーに `HEAD` 要求をマップできるようにすることにオプトインしています。</span><span class="sxs-lookup"><span data-stu-id="6163a-566">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="6163a-567">XSRF/CSRF および Razor ページ</span><span class="sxs-lookup"><span data-stu-id="6163a-567">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="6163a-568">[偽造防止検証](xref:security/anti-request-forgery)のためにコードを記述する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6163a-568">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="6163a-569">偽造防止トークンの生成と検証は、自動的に Razor ページに含まれます。</span><span class="sxs-lookup"><span data-stu-id="6163a-569">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="6163a-570">Razor ページでのレイアウト、パーシャル、テンプレート、およびタグ ヘルパーの使用</span><span class="sxs-lookup"><span data-stu-id="6163a-570">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="6163a-571">ページは、Razor ビュー エンジンのすべての機能で動作します。</span><span class="sxs-lookup"><span data-stu-id="6163a-571">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="6163a-572">レイアウト、パーシャル、テンプレート、タグ ヘルパー、" *_ViewStart.cshtml*"、" *_ViewImports.cshtml*" は、従来の Razor ビューと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="6163a-572">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="6163a-573">これらの機能の一部を利用してこのページをまとめてみましょう。</span><span class="sxs-lookup"><span data-stu-id="6163a-573">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="6163a-574">[レイアウト ページ](xref:mvc/views/layout)を *Pages/Shared/_Layout.cshtml* に追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-574">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="6163a-575">[レイアウト](xref:mvc/views/layout)は次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="6163a-575">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="6163a-576">(ページでレイアウトを止めない限り) 各ページのレイアウトを制御します。</span><span class="sxs-lookup"><span data-stu-id="6163a-576">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="6163a-577">JavaScript やスタイルシートなどの HTML 構造をインポートします。</span><span class="sxs-lookup"><span data-stu-id="6163a-577">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="6163a-578">詳細については、[レイアウトのページ](xref:mvc/views/layout)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-578">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="6163a-579">[Layout](xref:mvc/views/layout#specifying-a-layout) プロパティは *Pages/_ViewStart.cshtml* で設定されています。</span><span class="sxs-lookup"><span data-stu-id="6163a-579">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="6163a-580">レイアウトは、*Pages/Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-580">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="6163a-581">ページは現在のページと同じフォルダーから開始して、階層的に他のビュー (レイアウト、テンプレート、パーシャル) を検索します。</span><span class="sxs-lookup"><span data-stu-id="6163a-581">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="6163a-582">"*Pages/Shared*" フォルダー内のレイアウトは、"*Pages*" フォルダー配下の任意の Razor ページから使用できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-582">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="6163a-583">レイアウト ファイルは *Pages/Shared* フォルダーに入ります。</span><span class="sxs-lookup"><span data-stu-id="6163a-583">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="6163a-584">レイアウト ファイルを *Views/Shared* フォルダー内に配置**しない**ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6163a-584">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="6163a-585">*Views/Shared* は MVC ビュー パターンです。</span><span class="sxs-lookup"><span data-stu-id="6163a-585">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="6163a-586">Razor ページは、パス規則ではなく、フォルダー階層に依存することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="6163a-586">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="6163a-587">Razor ページからのビュー検索には、"*Pages*" フォルダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6163a-587">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="6163a-588">MVC コントローラーで使用しているレイアウト、テンプレート、およびパーシャルと、従来の Razor ビューは "*機能します*"。</span><span class="sxs-lookup"><span data-stu-id="6163a-588">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="6163a-589">*Pages/_ViewImports.cshtml* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-589">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="6163a-590">`@namespace` はこのチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="6163a-590">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="6163a-591">`@addTagHelper` ディレクティブにより、[組み込みタグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/Index)が *Pages* フォルダー内のすべてのページにもたらされます。</span><span class="sxs-lookup"><span data-stu-id="6163a-591">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="6163a-592">ページで `@namespace` ディレクティブが明示的に使用されている場合:</span><span class="sxs-lookup"><span data-stu-id="6163a-592">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="6163a-593">ディレクティブは、ページの名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-593">The directive sets the namespace for the page.</span></span> <span data-ttu-id="6163a-594">`@model` ディレクティブには、名前空間を含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6163a-594">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="6163a-595">`@namespace` ディレクティブが *_ViewImports.cshtml* に含まれていると、指定した名前空間が `@namespace` ディレクティブをインポートするページで生成された名前空間のプレフィックスを提供します。</span><span class="sxs-lookup"><span data-stu-id="6163a-595">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="6163a-596">生成された名前空間の残りの部分 (サフィックスの部分) は、 *_ViewImports.cshtml* を含むフォルダーとページを含むフォルダー間のドットで区切られた相対パスです。</span><span class="sxs-lookup"><span data-stu-id="6163a-596">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="6163a-597">たとえば、`PageModel` クラス *Pages/Customers/Edit.cshtml.cs* は名前空間を明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-597">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="6163a-598">*Pages/_ViewImports.cshtml* ファイルは次の名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-598">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="6163a-599">"*Pages/Customers/Edit.cshtml*" Razor ページの生成された名前空間は、`PageModel` クラスと同じです。</span><span class="sxs-lookup"><span data-stu-id="6163a-599">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="6163a-600">`@namespace` は "*従来の Razor ビューでも機能します。* "</span><span class="sxs-lookup"><span data-stu-id="6163a-600">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="6163a-601">元の *Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-601">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="6163a-602">更新された *Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="6163a-602">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="6163a-603">[Razor ページのスタート プロジェクト](#rpvs17)には、クライアント側の検証をフックする "*Pages/_ValidationScriptsPartial.cshtml*" が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-603">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="6163a-604">部分ビューの詳細については、「<xref:mvc/views/partial>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-604">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="6163a-605">ページの URL の生成</span><span class="sxs-lookup"><span data-stu-id="6163a-605">URL generation for Pages</span></span>

<span data-ttu-id="6163a-606">上に示した `Create` ページでは、`RedirectToPage` を使用します。</span><span class="sxs-lookup"><span data-stu-id="6163a-606">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="6163a-607">アプリには次のファイル/フォルダー構造があります。</span><span class="sxs-lookup"><span data-stu-id="6163a-607">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="6163a-608">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="6163a-608">*/Pages*</span></span>

  * <span data-ttu-id="6163a-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-609">*Index.cshtml*</span></span>
  * <span data-ttu-id="6163a-610">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="6163a-610">*/Customers*</span></span>

    * <span data-ttu-id="6163a-611">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-611">*Create.cshtml*</span></span>
    * <span data-ttu-id="6163a-612">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-612">*Edit.cshtml*</span></span>
    * <span data-ttu-id="6163a-613">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6163a-613">*Index.cshtml*</span></span>

<span data-ttu-id="6163a-614">成功すると、*Pages/Customers/Create.cshtml* ページと *Pages/Customers/Edit.cshtml* ページが *Pages/Index.cshtml* にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="6163a-614">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="6163a-615">文字列 `/Index` は前のページにアクセスするための URI の一部です。</span><span class="sxs-lookup"><span data-stu-id="6163a-615">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="6163a-616">文字列 `/Index` は、*Pages/Index.cshtml* ページへの URI を生成するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-616">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="6163a-617">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-617">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="6163a-618">ページ名は、先頭の `/` を含む、ルート */Pages* フォルダーからページへのパスです (たとえば `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="6163a-618">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="6163a-619">先述の URL 生成サンプルでは、URL のハードコーディングに関する拡張オプションと機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-619">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="6163a-620">URL の生成は[ルーティング](xref:mvc/controllers/routing)を使用し、ターゲット パスで定義されたルート方法に従って、パラメーターの生成とエンコードができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-620">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="6163a-621">ページの URL 生成は、相対名をサポートします。</span><span class="sxs-lookup"><span data-stu-id="6163a-621">URL generation for pages supports relative names.</span></span> <span data-ttu-id="6163a-622">次の表に、*Pages/Customers/Create.cshtml* の異なる `RedirectToPage` パラメーターで選択されたインデックス ページを示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-622">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="6163a-623">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="6163a-623">RedirectToPage(x)</span></span>| <span data-ttu-id="6163a-624">ページ</span><span class="sxs-lookup"><span data-stu-id="6163a-624">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="6163a-625">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="6163a-625">RedirectToPage("/Index")</span></span> | <span data-ttu-id="6163a-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="6163a-626">*Pages/Index*</span></span> |
| <span data-ttu-id="6163a-627">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="6163a-627">RedirectToPage("./Index");</span></span> | <span data-ttu-id="6163a-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="6163a-628">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="6163a-629">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="6163a-629">RedirectToPage("../Index")</span></span> | <span data-ttu-id="6163a-630">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="6163a-630">*Pages/Index*</span></span> |
| <span data-ttu-id="6163a-631">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="6163a-631">RedirectToPage("Index")</span></span>  | <span data-ttu-id="6163a-632">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="6163a-632">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="6163a-633">`RedirectToPage("Index")`、`RedirectToPage("./Index")`、および `RedirectToPage("../Index")` は*相対名*です。</span><span class="sxs-lookup"><span data-stu-id="6163a-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="6163a-634">`RedirectToPage` パラメーターは現在のページのパスと*組み合わされて*、ターゲット ページの名前を計算します。</span><span class="sxs-lookup"><span data-stu-id="6163a-634">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="6163a-635">相対名のリンクは、複雑な構造を持つサイトを構築する際に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6163a-635">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="6163a-636">相対名を使用してフォルダー内のページ間をリンクする場合、そのフォルダー名を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-636">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="6163a-637">すべてのリンクは引き続き機能します (リンクにはフォルダー名が含まれていないため)。</span><span class="sxs-lookup"><span data-stu-id="6163a-637">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="6163a-638">別の [[区分]](xref:mvc/controllers/areas) のページにリダイレクトするには、その区分を指定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-638">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="6163a-639">詳細については、「<xref:mvc/controllers/areas>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-639">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="6163a-640">ViewData 属性</span><span class="sxs-lookup"><span data-stu-id="6163a-640">ViewData attribute</span></span>

<span data-ttu-id="6163a-641">データは [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) とのページに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-641">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="6163a-642">`[ViewData]` 属性を持つコントローラーまたは Razor ページのモデルのプロパティの値は、[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) に格納してそこから読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-642">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="6163a-643">次の例では、`AboutModel` に `[ViewData]` でマークされた `Title` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6163a-643">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="6163a-644">`Title` プロパティは、[About] ページのタイトルに設定されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-644">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="6163a-645">[About] ページでは、モデル プロパティとして `Title` プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="6163a-645">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="6163a-646">レイアウトでは、タイトルは ViewData ディクショナリから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6163a-646">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="6163a-647">TempData</span><span class="sxs-lookup"><span data-stu-id="6163a-647">TempData</span></span>

<span data-ttu-id="6163a-648">ASP.NET Core は [コントローラー](/dotnet/api/microsoft.aspnetcore.mvc.controller)上で [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="6163a-648">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="6163a-649">このプロパティは、読み取られるまでデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="6163a-649">This property stores data until it's read.</span></span> <span data-ttu-id="6163a-650">`Keep` メソッドと `Peek` メソッドは、削除せずにデータを確認するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="6163a-650">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="6163a-651">`TempData` は、複数の要求にデータが必要な場合のリダイレクトに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6163a-651">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="6163a-652">次のコードは、`TempData` を使用して `Message` の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-652">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="6163a-653">*Pages/Customers/Index.cshtml* ファイル内の次のマークアップは、`TempData` を使用して `Message` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="6163a-653">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="6163a-654">*Pages/Customers/Index.cshtml.cs* ページは、`[TempData]` 属性を `Message` プロパティに適用します。</span><span class="sxs-lookup"><span data-stu-id="6163a-654">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="6163a-655">詳細については、「[TempData](xref:fundamentals/app-state#tempdata)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6163a-655">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="6163a-656">ページあたり複数のハンドラー</span><span class="sxs-lookup"><span data-stu-id="6163a-656">Multiple handlers per page</span></span>

<span data-ttu-id="6163a-657">次のページでは、`asp-page-handler` タグ ヘルパーを使用して 2 つのハンドラーにマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-657">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="6163a-658">前の例のフォームには、それぞれが `FormActionTagHelper` を使用して異なる URL に送信する 2 つの送信ボタンがあります。</span><span class="sxs-lookup"><span data-stu-id="6163a-658">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="6163a-659">`asp-page-handler` 属性は、`asp-page` のコンパニオンです。</span><span class="sxs-lookup"><span data-stu-id="6163a-659">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="6163a-660">`asp-page-handler` はページごとに定義されている各ハンドラー メソッドに送信する URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="6163a-660">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="6163a-661">サンプルは現在のページにリンクしているため、`asp-page` は指定されません。</span><span class="sxs-lookup"><span data-stu-id="6163a-661">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="6163a-662">ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="6163a-662">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="6163a-663">上記のコードは、*名前付きハンドラー メソッド*を使用しています。</span><span class="sxs-lookup"><span data-stu-id="6163a-663">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="6163a-664">名前付きハンドラー メソッドは、名前の `On<HTTP Verb>` の後および `Async` の前 (ある場合) のテキストを取得して作成されます。</span><span class="sxs-lookup"><span data-stu-id="6163a-664">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="6163a-665">前の例では、ページ メソッドは OnPost**JoinList**Async と OnPost**JoinListUC**Async です。</span><span class="sxs-lookup"><span data-stu-id="6163a-665">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="6163a-666">*OnPost* と *Async* を削除すると、ハンドラー名は `JoinList` と `JoinListUC` になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-666">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="6163a-667">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-667">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="6163a-668">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="6163a-668">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="6163a-669">カスタム ルート</span><span class="sxs-lookup"><span data-stu-id="6163a-669">Custom routes</span></span>

<span data-ttu-id="6163a-670">`@page` ディレクティブを次に使用します:</span><span class="sxs-lookup"><span data-stu-id="6163a-670">Use the `@page` directive to:</span></span>

* <span data-ttu-id="6163a-671">カスタム ルートをページに指定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-671">Specify a custom route to a page.</span></span> <span data-ttu-id="6163a-672">たとえば、[バージョン情報] ページへのルートを `@page "/Some/Other/Path"` を使用して `/Some/Other/Path` に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-672">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="6163a-673">ページの既定のルートにセグメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-673">Append segments to a page's default route.</span></span> <span data-ttu-id="6163a-674">たとえば、"item" セグメントを `@page "item"` を使用してページの既定のルートに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-674">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="6163a-675">ページの既定のルートにパラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-675">Append parameters to a page's default route.</span></span> <span data-ttu-id="6163a-676">たとえば、`@page "{id}"` を含むページに ID パラメーター `id` を必須とすることができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-676">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="6163a-677">パスの先頭のチルダ (`~`) によって指定されたルートの相対パスがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6163a-677">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="6163a-678">たとえば、`@page "~/Some/Other/Path"` は `@page "/Some/Other/Path"` と同じです。</span><span class="sxs-lookup"><span data-stu-id="6163a-678">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="6163a-679">URL 内のクエリ文字列 `?handler=JoinList` が気に入らない場合は、ルートを変更して URL のパス部分にハンドラー名を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-679">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="6163a-680">`@page` ディレクティブの後に二重引用符で囲んだルート テンプレートを追加して、ルートをカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-680">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="6163a-681">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="6163a-681">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="6163a-682">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="6163a-682">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="6163a-683">`handler` の後の `?` は、ルート パラメーターが省略可能なことを意味します。</span><span class="sxs-lookup"><span data-stu-id="6163a-683">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="6163a-684">構成と設定</span><span class="sxs-lookup"><span data-stu-id="6163a-684">Configuration and settings</span></span>

<span data-ttu-id="6163a-685">高度なオプションを構成するには、MVC ビルダーで拡張メソッド `AddRazorPagesOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="6163a-685">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="6163a-686">現在は、`RazorPagesOptions` を使用してページのルート ディレクトリを設定したり、ページのアプリケーション モデルの規則を追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="6163a-686">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="6163a-687">将来、この方法でより多くの機能拡張を可能にしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="6163a-687">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="6163a-688">ビューをプリコンパイルするには、[Razor ビューのコンパイル](xref:mvc/views/view-compilation)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6163a-688">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="6163a-689">[サンプル コードをダウンロードまたは表示します](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample)。</span><span class="sxs-lookup"><span data-stu-id="6163a-689">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="6163a-690">この概要に基づく、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するページをご覧ください</span><span class="sxs-lookup"><span data-stu-id="6163a-690">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="6163a-691">Razor Pages をコンテンツのルートに指定する</span><span class="sxs-lookup"><span data-stu-id="6163a-691">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="6163a-692">Razor Pages のルートは既定で */Pages* ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="6163a-692">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="6163a-693">[WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) を [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) に追加して、Razor Pages をアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) に置くように指定します。</span><span class="sxs-lookup"><span data-stu-id="6163a-693">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="6163a-694">Razor Pages をカスタム ルート ディレクトリに指定する</span><span class="sxs-lookup"><span data-stu-id="6163a-694">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="6163a-695">(相対パスを指定して) Razor Pages をアプリのカスタム ルート ディレクトリに指定するには、[WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) を [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) に追加します。</span><span class="sxs-lookup"><span data-stu-id="6163a-695">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="6163a-696">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6163a-696">Additional resources</span></span>

* [<span data-ttu-id="6163a-697">属性と Razor ページを承認する</span><span class="sxs-lookup"><span data-stu-id="6163a-697">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
