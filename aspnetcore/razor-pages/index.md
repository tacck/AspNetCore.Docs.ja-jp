---
title: ASP.NET Core での Razor ページの概要
author: Rick-Anderson
description: ASP.NET Core の Razor Pages を使用して、ページのコーディングに重点を置いたシナリオをより簡略化して、MVC を使用する場合よりも生産性を高める方法について説明します。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 6939d285838a6dd971f530c1d65d73273b5b14e7
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424561"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="ffe9d-103">ASP.NET Core での Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="ffe9d-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ffe9d-104">[Rick Anderson](https://twitter.com/RickAndMSFT) および [Ryan Nowak](https://github.com/rynowak) 著</span><span class="sxs-lookup"><span data-stu-id="ffe9d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="ffe9d-105">Razor ページを利用することで、ページのコーディングに今まで以上に集中できます。また、コントローラーとビューを使用する場合より生産的になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="ffe9d-106">モデル ビュー コントローラーのアプローチを使用するチュートリアルをお探しの場合は、「[Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc)」 (ASP.NET Core MVC の概要) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="ffe9d-107">このドキュメントでは、Razor ページの概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="ffe9d-108">手順を追って説明するチュートリアルではありません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="ffe9d-109">セクションの一部を理解できない場合は、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="ffe9d-110">ASP.NET Core の概要については、「[ASP.NET Core の概要](xref:index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ffe9d-111">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ffe9d-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffe9d-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffe9d-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffe9d-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffe9d-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffe9d-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ffe9d-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="ffe9d-115">Razor ページ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ffe9d-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffe9d-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffe9d-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ffe9d-117">Razor ページ プロジェクトを作成する詳細な手順については、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffe9d-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffe9d-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ffe9d-119">コマンド ラインから `dotnet new webapp` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffe9d-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ffe9d-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ffe9d-121">Razor ページ プロジェクトを作成する詳細な手順については、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="ffe9d-122">Razor ページ</span><span class="sxs-lookup"><span data-stu-id="ffe9d-122">Razor Pages</span></span>

<span data-ttu-id="ffe9d-123">Razor ページは *Startup.cs* で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="ffe9d-124">基本ページを検討します。<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="ffe9d-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="ffe9d-125">上記のコードは、コントローラーとビューを含んだ ASP.NET Core アプリで使われる [Razor ビュー ファイル](xref:tutorials/first-mvc-app/adding-view)によく似ています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="ffe9d-126">違いは [`@page`](xref:mvc/views/razor#page) ディレクティブにあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="ffe9d-127">`@page` はファイルを MVC アクションにします。つまり、コントローラーを経由せずに要求を直接処理します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="ffe9d-128">`@page` はページで最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="ffe9d-129">`@page` はその他の [Razor](xref:mvc/views/razor) コンストラクトの動作に影響します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="ffe9d-130">Razor Pages ファイル名には *.cshtml* サフィックスが付きます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="ffe9d-131">`PageModel` クラスを使用している類似したページが、次の 2 つのファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="ffe9d-132">*Pages/Index2.cshtml* ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="ffe9d-133">*Pages/Index2.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="ffe9d-134">規則により、`PageModel` クラス ファイルは、Razor ページ ファイルと同じ名前に *.cs* が付加された名前になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="ffe9d-135">たとえば、上の Razor ページは *Pages/Index2.cshtml* になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="ffe9d-136">`PageModel` クラスを含むファイル名は、*Pages/Index2.cshtml.cs* になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="ffe9d-137">URL パスのページへの関連付けは、ファイル システム内のページの場所によって決定されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="ffe9d-138">次の表に、Razor ページ パスと一致 URL を示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="ffe9d-139">ファイル名とパス</span><span class="sxs-lookup"><span data-stu-id="ffe9d-139">File name and path</span></span>               | <span data-ttu-id="ffe9d-140">一致 URL</span><span class="sxs-lookup"><span data-stu-id="ffe9d-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="ffe9d-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="ffe9d-142">`/` または `/Index`</span><span class="sxs-lookup"><span data-stu-id="ffe9d-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="ffe9d-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="ffe9d-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="ffe9d-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="ffe9d-146">`/Store` または `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="ffe9d-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="ffe9d-147">メモ:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-147">Notes:</span></span>

* <span data-ttu-id="ffe9d-148">既定では、ランタイムが *Pages* フォルダー内で Razor ページ ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="ffe9d-149">`Index` は、URL にページが含まれない場合の既定のページになります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="ffe9d-150">基本フォームを作成する</span><span class="sxs-lookup"><span data-stu-id="ffe9d-150">Write a basic form</span></span>

<span data-ttu-id="ffe9d-151">Razor ページは、アプリの構築時に Web ブラウザーで使用される一般的なパターンを実装しやすくするために設計されています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="ffe9d-152">[モデル バインド](xref:mvc/models/model-binding)、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、および HTML ヘルパーはすべて、Razor ページ クラスで定義されたプロパティで*機能します*。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="ffe9d-153">`Contact` モデルの基本的な "お問い合わせ" フォームを実装するページを考察します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="ffe9d-154">このドキュメントのサンプルでは、[Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) ファイルで `DbContext` が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="ffe9d-155">データ モデル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="ffe9d-156">db コンテキスト:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="ffe9d-157">*Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="ffe9d-158">*Pages/Create.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="ffe9d-159">規則により、`PageModel` クラスは `<PageName>Model` と呼ばれ、ページと同じ名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="ffe9d-160">`PageModel` クラスでは、ページの表示からロジックを分離できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="ffe9d-161">これは、ページに送信される要求のページ ハンドラーと、ページのレンダリングに使用されるデータを定義します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="ffe9d-162">この分離により可能になること:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-162">This separation allows:</span></span>

* <span data-ttu-id="ffe9d-163">[依存関係の挿入](xref:fundamentals/dependency-injection)によるページの依存関係の管理。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="ffe9d-164">単体テスト</span><span class="sxs-lookup"><span data-stu-id="ffe9d-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="ffe9d-165">このページには、(ユーザーがフォームを投稿したときに) `POST` 要求で実行される `OnPostAsync` "*ハンドラー メソッド*" があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="ffe9d-166">任意の HTTP 動詞のハンドラー メソッドを追加できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="ffe9d-167">最も一般的なハンドラーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-167">The most common handlers are:</span></span>

* <span data-ttu-id="ffe9d-168">ページに必要な状態を初期化するための `OnGet`。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="ffe9d-169">前のコードでは、`OnGet` メソッドにより *CreateModel.cshtml* Razor ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="ffe9d-170">フォームの送信を処理するための `OnPost`。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="ffe9d-171">`Async` 名前付けサフィックスは省略可能ですが、非同期関数の規則でよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="ffe9d-172">上記のコードは、Razor ページでは一般的です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="ffe9d-173">コントローラーとビューを利用する ASP.NET アプリに慣れている場合:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="ffe9d-174">前の例の `OnPostAsync` コードは、一般的なコントローラー コードに似ています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="ffe9d-175">[モデル バインド](xref:mvc/models/model-binding)、[検証](xref:mvc/models/validation)、アクションの結果など、MVC プリミティブのほとんどは Controllers ページや Razor ページと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="ffe9d-176">上記の `OnPostAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="ffe9d-177">`OnPostAsync` の基本的な流れは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="ffe9d-178">検証エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-178">Check for validation errors.</span></span>

* <span data-ttu-id="ffe9d-179">エラーがない場合は、データを保存し、リダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="ffe9d-180">エラーがある場合は、検証メッセージとともにページをもう一度表示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="ffe9d-181">多くの場合、検証エラーはクライアントで検出され、サーバーには送信されません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="ffe9d-182">*Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="ffe9d-183">*Pages/Create.cshtml* からレンダリングされた HTML:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="ffe9d-184">前のコードで投稿したフォーム:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="ffe9d-185">有効なデータ:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-185">With valid data:</span></span>

  * <span data-ttu-id="ffe9d-186">`OnPostAsync` ハンドラー メソッドにより <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> ヘルパー メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="ffe9d-187">`RedirectToPage` は <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="ffe9d-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="ffe9d-189">はアクションの結果です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-189">Is an action result.</span></span>
    * <span data-ttu-id="ffe9d-190">は、(コントローラーやビューで使用される) `RedirectToAction` や`RedirectToRoute` に似ています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="ffe9d-191">はページ用にカスタマイズされています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-191">Is customized for pages.</span></span> <span data-ttu-id="ffe9d-192">上記のサンプルでは、ルート インデックス ページ (`/Index`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="ffe9d-193">`RedirectToPage` については、「[ページの URL の生成](#url_gen)」セクションで詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="ffe9d-194">サーバーに検証エラーが渡される:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="ffe9d-195">`OnPostAsync` ハンドラー メソッドにより <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> ヘルパー メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="ffe9d-196">`Page` は <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="ffe9d-197">`Page` を返すのは、コントローラーのアクションが `View` を返す方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="ffe9d-198">`PageResult` はハンドラー メソッドの既定の戻り値の型です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="ffe9d-199">`void` を返すハンドラー メソッドがページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="ffe9d-200">前の例では、値のないフォームが投稿された結果、[ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) から false が返されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="ffe9d-201">このサンプルでは、検証エラーはクライアントに表示されません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="ffe9d-202">検証エラーの処理については、このドキュメントの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="ffe9d-203">クライアント側の検証で検証エラーが検出される:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="ffe9d-204">データはサーバーに投稿されて**いません**。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="ffe9d-205">クライアント側の検証については、このドキュメントの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="ffe9d-206">`Customer` プロパティは [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性を使用してモデル バインドにオプトインします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="ffe9d-207">`[BindProperty]` は、クライアントが変更するべきではないプロパティを含むモデルで使用**しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="ffe9d-208">詳細については、「[過剰ポスティング](xref:data/ef-rp/crud#overposting)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="ffe9d-209">既定では、Razor Pages はプロパティを非 `GET` 動詞とのみバインドします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-209">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="ffe9d-210">プロパティにバインドすると、HTTP データをモデル型に変換する目的でコードを記述する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="ffe9d-211">同じプロパティを使用してバインドすることでコードを減らし、フィールド (`<input asp-for="Customer.Name">`) からレンダリングして入力を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="ffe9d-212">*Pages/Create.cshtml* ビュー ファイルのレビュー:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="ffe9d-213">前のコードでは、[入力タグ ヘルパー](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` によって HTML `<input>` 要素が `Customer.Name` モデル式にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="ffe9d-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) でタグ ヘルパーを使用可能にします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="ffe9d-215">ホーム ページ</span><span class="sxs-lookup"><span data-stu-id="ffe9d-215">The home page</span></span>

<span data-ttu-id="ffe9d-216">*Index.cshtml* はホーム ページです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="ffe9d-217">関連付けられた `PageModel` クラス (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="ffe9d-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ffe9d-218">*Index.cshtml* ファイルには、次のマークアップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="ffe9d-219">`<a /a>` [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)では、`asp-route-{value}` 属性を使用して編集ページへのリンクが生成されました。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="ffe9d-220">リンクには、連絡先 ID とともにルート データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="ffe9d-221">たとえば、`https://localhost:5001/Edit/1` のようにします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="ffe9d-222">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="ffe9d-223">*Index.cshtml* ファイルには、各顧客の連絡先の削除ボタンを作成するマークアップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="ffe9d-224">レンダリングされた HTML:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="ffe9d-225">HTML で削除ボタンがレンダリングされる場合、その [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) には次のパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="ffe9d-226">`asp-route-id` 属性によって指定された顧客の連絡先 ID。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="ffe9d-227">`asp-page-handler` 属性によって指定された `handler`。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="ffe9d-228">ボタンが選択されると、フォームの `POST` 要求がサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="ffe9d-229">慣例により、ハンドラー メソッドの名前はスキーム `OnPost[handler]Async` に従った `handler` パラメーターの値に基づいて選択されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="ffe9d-230">この例では `handler` が `delete` であるため、`OnPostDeleteAsync` ハンドラー メソッドを使用して `POST` 要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="ffe9d-231">`asp-page-handler` が `remove` などの別の値に設定されている場合、名前が `OnPostRemoveAsync` のハンドラー メソッドが選択されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="ffe9d-232">`OnPostDeleteAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="ffe9d-233">クエリ文字列から `id` を取得します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="ffe9d-234">`FindAsync` を使用してデータベースから顧客の連絡先を照会します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="ffe9d-235">顧客の連絡先が見つからない場合、それは削除されており、データベースが更新されています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="ffe9d-236">ルート インデックス ページ (`/Index`) にリダイレクトされるように、<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="ffe9d-237">Edit.cshtml ファイル</span><span class="sxs-lookup"><span data-stu-id="ffe9d-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="ffe9d-238">最初の行には `@page "{id:int}"` ディレクティブが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="ffe9d-239">ルーティングの制約 `"{id:int}"` は、`int` ルート データを含むページへの要求を受け入れるようにページに指示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="ffe9d-240">ページへの要求に `int` に変換できるルート データが含まれていない場合は、ランタイムで HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="ffe9d-241">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="ffe9d-242">*Edit.cshtml.cs* ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="ffe9d-243">検証</span><span class="sxs-lookup"><span data-stu-id="ffe9d-243">Validation</span></span>

<span data-ttu-id="ffe9d-244">検証規則:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-244">Validation rules:</span></span>

* <span data-ttu-id="ffe9d-245">はモデル クラスで指定 (宣言) されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="ffe9d-246">はアプリ内のあらゆる場所で適用されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="ffe9d-247"><xref:System.ComponentModel.DataAnnotations> 名前空間には、クラスまたはプロパティに宣言的に適用される一連の組み込みの検証属性があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="ffe9d-248">また、DataAnnotations には、書式設定を支援し、どの検証を行わない [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) のような書式設定属性もあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="ffe9d-249">`Customer` モデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="ffe9d-250">次の *Create.cshtml* ビュー ファイルを使用:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="ffe9d-251">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-251">The preceding code:</span></span>

* <span data-ttu-id="ffe9d-252">jQuery と jQuery 検証スクリプトが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="ffe9d-253">`<div />` と `<span />` [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使用して次を有効にします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="ffe9d-254">クライアント側の検証。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-254">Client-side validation.</span></span>
  * <span data-ttu-id="ffe9d-255">検証エラー レンダリング。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-255">Validation error rendering.</span></span>

* <span data-ttu-id="ffe9d-256">次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="ffe9d-257">名前値なしで Create フォームを投稿すると、このフォームに "The Name field is required." (名前フィールドは必須です。) というエラー メッセージ</span><span class="sxs-lookup"><span data-stu-id="ffe9d-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="ffe9d-258">が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-258">on the form.</span></span> <span data-ttu-id="ffe9d-259">JavaScript がクライアントで有効になっている場合、サーバーに投稿されず、エラーがブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="ffe9d-260">`[StringLength(10)]` 属性によって、レンダリングされた HTML で `data-val-length-max="10"` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="ffe9d-261">`data-val-length-max` により、指定の最大長を超える入力がブラウザーで禁止されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="ffe9d-262">[Fiddler](https://www.telerik.com/fiddler) のようなツールを投稿の編集と返信に使用する場合:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="ffe9d-263">名前の長さ値が 10 を超えています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-263">With the name longer than 10.</span></span>
* <span data-ttu-id="ffe9d-264">"The field Name must be a string with a maximum length of 10." (名前フィールドは最大長が 10 の文字列になります。) というエラー メッセージが</span><span class="sxs-lookup"><span data-stu-id="ffe9d-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="ffe9d-265">返されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-265">is returned.</span></span>

<span data-ttu-id="ffe9d-266">次の `Movie` モデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="ffe9d-267">検証属性では、適用対象のモデル プロパティに適用する動作が指定されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="ffe9d-268">`Required` および `MinimumLength` 属性は、プロパティに値が必要であることを示します。ただし、この検証を満たすためにユーザーが空白を入力することは禁止されていません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="ffe9d-269">`RegularExpression` 属性は、入力できる文字を制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="ffe9d-270">上のコード "Genre" では:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="ffe9d-271">文字のみを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-271">Must only use letters.</span></span>
  * <span data-ttu-id="ffe9d-272">最初の文字は大文字にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="ffe9d-273">空白、数字、特殊文字は使用できません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="ffe9d-274">`RegularExpression` "評価":</span><span class="sxs-lookup"><span data-stu-id="ffe9d-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="ffe9d-275">最初の文字が大文字である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="ffe9d-276">後続のスペースでは、特殊文字と数字が使用できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="ffe9d-277">"PG-13" は評価に対して有効ですが、"Genre" に対しては失敗します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="ffe9d-278">`Range` 属性は、指定した範囲内に値を制限します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="ffe9d-279">`StringLength` 属性により、文字列プロパティの最大長が設定されます。任意で最小長も設定できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="ffe9d-280">値の型 (`decimal`、`int`、`float`、`DateTime` など) は本質的に必須ではなく、`[Required]` 属性を必要としません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="ffe9d-281">`Movie` モデルの [作成] ページには、エラーと無効な値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![複数 jQuery クライアント側検証エラーが表示されたムービー ビュー フォーム](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="ffe9d-283">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-283">For more information, see:</span></span>

* [<span data-ttu-id="ffe9d-284">Movie アプリに検証を追加する</span><span class="sxs-lookup"><span data-stu-id="ffe9d-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="ffe9d-285">[ASP.NET Core のモデル検証](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="ffe9d-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="ffe9d-286">OnGet ハンドラー フォールバックを使用した HEAD 要求の処理</span><span class="sxs-lookup"><span data-stu-id="ffe9d-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="ffe9d-287">`HEAD` 要求により、特定のリソースのヘッダーを取得できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="ffe9d-288">`GET` 要求とは異なり、`HEAD` 要求から応答本文は返されません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="ffe9d-289">通常、`HEAD` 要求に対して `OnHead` ハンドラーが作成され、呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="ffe9d-290">`OnHead` ハンドラーが定義されていない場合、Razor Pages は `OnGet` ハンドラーの呼び出しにフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-290">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="ffe9d-291">XSRF/CSRF と Razor ページ</span><span class="sxs-lookup"><span data-stu-id="ffe9d-291">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="ffe9d-292">Razor Pages は、[偽造防止検証](xref:security/anti-request-forgery)によって保護されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-292">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="ffe9d-293">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) により HTML フォーム要素に偽造防止トークンが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="ffe9d-294">Razor ページでのレイアウト、パーシャル、テンプレート、およびタグ ヘルパーの使用</span><span class="sxs-lookup"><span data-stu-id="ffe9d-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="ffe9d-295">ページは、Razor ビュー エンジンのすべての機能で動作します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="ffe9d-296">レイアウト、パーシャル、テンプレート、タグ ヘルパー、 *_ViewStart.cshtml*、 *_ViewImports.cshtml* は、従来の Razor ビューの場合と同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="ffe9d-297">これらの機能の一部を利用してこのページをまとめてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="ffe9d-298">[レイアウト ページ](xref:mvc/views/layout)を *Pages/Shared/_Layout.cshtml* に追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="ffe9d-299">[レイアウト](xref:mvc/views/layout)は次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="ffe9d-300">(ページでレイアウトを止めない限り) 各ページのレイアウトを制御します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="ffe9d-301">JavaScript やスタイルシートなどの HTML 構造をインポートします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="ffe9d-302">`@RenderBody()` が呼び出されるところで Razor ページの内容が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="ffe9d-303">詳細については、[レイアウトに関するページ](xref:mvc/views/layout)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="ffe9d-304">[Layout](xref:mvc/views/layout#specifying-a-layout) プロパティは *Pages/_ViewStart.cshtml* で設定されています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="ffe9d-305">レイアウトは、*Pages/Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="ffe9d-306">ページは現在のページと同じフォルダーから開始して、階層的に他のビュー (レイアウト、テンプレート、パーシャル) を検索します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="ffe9d-307">*Pages/Shared* フォルダー内のレイアウトは、*Pages* フォルダー配下の任意の Razor ページから使用できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="ffe9d-308">レイアウト ファイルは *Pages/Shared* フォルダーに入ります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="ffe9d-309">レイアウト ファイルを *Views/Shared* フォルダー内に配置**しない**ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="ffe9d-310">*Views/Shared* は MVC ビュー パターンです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-310">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="ffe9d-311">Razor ページは、パス規則ではなく、フォルダー階層に依存することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-311">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="ffe9d-312">Razor ページからのビュー検索には、*Pages* フォルダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="ffe9d-313">MVC コントローラーで使用されているレイアウト、テンプレート、パーシャルと、従来の Razor ビューは*機能します*。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="ffe9d-314">*Pages/_ViewImports.cshtml* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="ffe9d-315">`@namespace` はこのチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="ffe9d-316">`@addTagHelper` ディレクティブにより、[組み込みタグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/Index)が *Pages* フォルダー内のすべてのページにもたらされます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="ffe9d-317">ページに設定される `@namespace` ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="ffe9d-318">`@namespace` ディレクティブは、ページの名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="ffe9d-319">`@model` ディレクティブには、名前空間を含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="ffe9d-320">`@namespace` ディレクティブが *_ViewImports.cshtml* に含まれていると、指定した名前空間が `@namespace` ディレクティブをインポートするページで生成された名前空間のプレフィックスを提供します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="ffe9d-321">生成された名前空間の残りの部分 (サフィックスの部分) は、 *_ViewImports.cshtml* を含むフォルダーとページを含むフォルダー間のドットで区切られた相対パスです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="ffe9d-322">たとえば、`PageModel` クラス *Pages/Customers/Edit.cshtml.cs* は名前空間を明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="ffe9d-323">*Pages/_ViewImports.cshtml* ファイルは次の名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="ffe9d-324">*Pages/Customers/Edit.cshtml* Razor ページの生成された名前空間は、`PageModel` クラスと同じです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="ffe9d-325">`@namespace` は "*従来の Razor ビューでも機能します。* "</span><span class="sxs-lookup"><span data-stu-id="ffe9d-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="ffe9d-326">*Pages/Create.cshtml* ビュー ファイル を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="ffe9d-327">更新後の *Pages/Create.cshtml* ビュー ファイル、 *_ViewImports.cshtml*、前のレイアウト ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="ffe9d-328">前のコードでは、 *_ViewImports.cshtml* によって名前空間とタグ ヘルパーがインポートされました。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="ffe9d-329">レイアウト ファイルによって JavaScript ファイルがインポートされました。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="ffe9d-330">[Razor ページのスタート プロジェクト](#rpvs17)には、クライアント側の検証をフックする *Pages/_ValidationScriptsPartial.cshtml* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="ffe9d-331">部分ビューの詳細については、「<xref:mvc/views/partial>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="ffe9d-332">ページの URL の生成</span><span class="sxs-lookup"><span data-stu-id="ffe9d-332">URL generation for Pages</span></span>

<span data-ttu-id="ffe9d-333">上に示した `Create` ページでは、`RedirectToPage` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="ffe9d-334">アプリには次のファイル/フォルダー構造があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="ffe9d-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-335">*/Pages*</span></span>

  * <span data-ttu-id="ffe9d-336">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="ffe9d-337">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="ffe9d-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-338">*/Customers*</span></span>

    * <span data-ttu-id="ffe9d-339">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="ffe9d-340">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="ffe9d-341">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-341">*Index.cshtml*</span></span>

<span data-ttu-id="ffe9d-342">成功すると、*Pages/Customers/Create.cshtml* ページと *Pages/Customers/Edit.cshtml* ページが *Pages/Customers/Index.cshtml* にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="ffe9d-343">文字列 `./Index` は、前のページにアクセスするために使用される相対ページ名です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="ffe9d-344">これは、*Pages/Customers/Index.cshtml* ページへの URI を生成するために使われます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="ffe9d-345">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="ffe9d-346">絶対ページ名 `/Index` は、*Pages/Index.cshtml* ページへの URL を生成するために使われます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="ffe9d-347">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="ffe9d-348">ページ名は、先頭の `/` を含む、ルート */Pages* フォルダーからページへのパスです (たとえば `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="ffe9d-349">先述の URL 生成サンプルでは、URL のハードコーディングに関する拡張オプションと機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="ffe9d-350">URL の生成は[ルーティング](xref:mvc/controllers/routing)を使用し、ターゲット パスで定義されたルート方法に従って、パラメーターの生成とエンコードができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="ffe9d-351">ページの URL 生成は、相対名をサポートします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="ffe9d-352">次の表に、*Pages/Customers/Create.cshtml* の異なる `RedirectToPage` パラメーターで選択されたインデックス ページを示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="ffe9d-353">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="ffe9d-353">RedirectToPage(x)</span></span>| <span data-ttu-id="ffe9d-354">ページ</span><span class="sxs-lookup"><span data-stu-id="ffe9d-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="ffe9d-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="ffe9d-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="ffe9d-356">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-356">*Pages/Index*</span></span> |
| <span data-ttu-id="ffe9d-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="ffe9d-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="ffe9d-358">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="ffe9d-359">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="ffe9d-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="ffe9d-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-360">*Pages/Index*</span></span> |
| <span data-ttu-id="ffe9d-361">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="ffe9d-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="ffe9d-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="ffe9d-363">`RedirectToPage("Index")`、`RedirectToPage("./Index")`、`RedirectToPage("../Index")` は*相対名*です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="ffe9d-364">`RedirectToPage` パラメーターは現在のページのパスと*組み合わされて*、ターゲット ページの名前を計算します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="ffe9d-365">相対名のリンクは、複雑な構造を持つサイトを構築する際に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="ffe9d-366">あるフォルダー内のページ間をリンクする目的で相対名を使用するとき:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="ffe9d-367">フォルダー名を変更しても相対リンクは壊れません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="ffe9d-368">フォルダー名が含まれていないため、リンクは壊れません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="ffe9d-369">別の [[区分]](xref:mvc/controllers/areas) のページにリダイレクトするには、その区分を指定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="ffe9d-370">詳細については、次のトピックを参照してください。 <xref:mvc/controllers/areas> および <xref:razor-pages/razor-pages-conventions></span><span class="sxs-lookup"><span data-stu-id="ffe9d-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="ffe9d-371">ViewData 属性</span><span class="sxs-lookup"><span data-stu-id="ffe9d-371">ViewData attribute</span></span>

<span data-ttu-id="ffe9d-372">データは <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> を含むページに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="ffe9d-373">`[ViewData]` 属性を含むプロパティにはその値が格納され、<xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="ffe9d-374">次の例では、`AboutModel` により、`[ViewData]` 属性が `Title` プロパティに適用されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="ffe9d-375">[About] ページでは、モデル プロパティとして `Title` プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="ffe9d-376">レイアウトでは、タイトルは ViewData ディクショナリから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="ffe9d-377">TempData</span><span class="sxs-lookup"><span data-stu-id="ffe9d-377">TempData</span></span>

<span data-ttu-id="ffe9d-378">ASP.NET Core により <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> が公開されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="ffe9d-379">このプロパティは、読み取られるまでデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-379">This property stores data until it's read.</span></span> <span data-ttu-id="ffe9d-380"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> メソッドと <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> メソッドは、削除せずにデータを確認するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="ffe9d-381">`TempData` は、複数の要求に対してデータが必要な場合のリダイレクトに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="ffe9d-382">次のコードは、`TempData` を使用して `Message` の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="ffe9d-383">*Pages/Customers/Index.cshtml* ファイル内の次のマークアップは、`TempData` を使用して `Message` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="ffe9d-384">*Pages/Customers/Index.cshtml.cs* ページは、`[TempData]` 属性を `Message` プロパティに適用します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="ffe9d-385">詳細については、「[TempData](xref:fundamentals/app-state#tempdata)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="ffe9d-386">ページあたり複数のハンドラー</span><span class="sxs-lookup"><span data-stu-id="ffe9d-386">Multiple handlers per page</span></span>

<span data-ttu-id="ffe9d-387">次のページでは、`asp-page-handler` タグ ヘルパーを使用して 2 つのハンドラーにマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="ffe9d-388">前の例のフォームには、それぞれが `FormActionTagHelper` を使用して異なる URL に送信する 2 つの送信ボタンがあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="ffe9d-389">`asp-page-handler` 属性は、`asp-page` のコンパニオンです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="ffe9d-390">`asp-page-handler` はページごとに定義されている各ハンドラー メソッドに送信する URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="ffe9d-391">サンプルは現在のページにリンクしているため、`asp-page` は指定されません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="ffe9d-392">ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="ffe9d-393">上記のコードは、*名前付きハンドラー メソッド*を使用しています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="ffe9d-394">名前付きハンドラー メソッドは、名前の `On<HTTP Verb>` の後および `Async` の前 (ある場合) のテキストを取得して作成されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="ffe9d-395">前の例では、ページ メソッドは OnPost**JoinList**Async と OnPost**JoinListUC**Async です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="ffe9d-396">*OnPost* と *Async* を削除すると、ハンドラー名は `JoinList` と `JoinListUC` になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="ffe9d-397">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="ffe9d-398">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="ffe9d-399">カスタム ルート</span><span class="sxs-lookup"><span data-stu-id="ffe9d-399">Custom routes</span></span>

<span data-ttu-id="ffe9d-400">`@page` ディレクティブを次に使用します:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="ffe9d-401">カスタム ルートをページに指定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-401">Specify a custom route to a page.</span></span> <span data-ttu-id="ffe9d-402">たとえば、[バージョン情報] ページへのルートを `@page "/Some/Other/Path"` を使用して `/Some/Other/Path` に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="ffe9d-403">ページの既定のルートにセグメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-403">Append segments to a page's default route.</span></span> <span data-ttu-id="ffe9d-404">たとえば、"item" セグメントを `@page "item"` を使用してページの既定のルートに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="ffe9d-405">ページの既定のルートにパラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="ffe9d-406">たとえば、`@page "{id}"` を含むページに ID パラメーター `id` を必須とすることができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="ffe9d-407">パスの先頭のチルダ (`~`) によって指定されたルートの相対パスがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="ffe9d-408">たとえば、`@page "~/Some/Other/Path"` は `@page "/Some/Other/Path"` と同じです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="ffe9d-409">URL 内のクエリ文字列 `?handler=JoinList` が気に入らない場合は、ルートを変更して URL のパス部分にハンドラー名を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="ffe9d-410">`@page` ディレクティブの後に二重引用符で囲んだルート テンプレートを追加して、ルートをカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="ffe9d-411">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="ffe9d-412">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="ffe9d-413">`handler` の後の `?` は、ルート パラメーターが省略可能なことを意味します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="ffe9d-414">詳細な構成と設定</span><span class="sxs-lookup"><span data-stu-id="ffe9d-414">Advanced configuration and settings</span></span>

<span data-ttu-id="ffe9d-415">次のセクションの構成と設定はほとんどのアプリで必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="ffe9d-416">高度なオプションを構成するには、拡張メソッド <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="ffe9d-417"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> を使用してページのルート ディレクトリを設定したり、ページのアプリケーション モデルの規則を追加したりできます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="ffe9d-418">規則の詳細については、「[Razor ページの承認規則](xref:security/authorization/razor-pages-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="ffe9d-419">ビューをプリコンパイルするには、[Razor ビューのコンパイル](xref:mvc/views/view-compilation)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="ffe9d-420">Razor ページをコンテンツのルートに指定する</span><span class="sxs-lookup"><span data-stu-id="ffe9d-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="ffe9d-421">Razor ページのルートは既定で */Pages* ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="ffe9d-422"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> を追加して、Razor Pages をアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) に置くように指定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="ffe9d-423">Razor ページをカスタム ルート ディレクトリに指定する</span><span class="sxs-lookup"><span data-stu-id="ffe9d-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="ffe9d-424">(相対パスを指定して) Razor ページをアプリのカスタム ルート ディレクトリに置くように指定するには、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> を追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="ffe9d-425">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ffe9d-425">Additional resources</span></span>

* <span data-ttu-id="ffe9d-426">この概要に基づく、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するページをご覧ください</span><span class="sxs-lookup"><span data-stu-id="ffe9d-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="ffe9d-427">サンプル コードのダウンロードまたは表示</span><span class="sxs-lookup"><span data-stu-id="ffe9d-427">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ffe9d-428">[Rick Anderson](https://twitter.com/RickAndMSFT) および [Ryan Nowak](https://github.com/rynowak) 著</span><span class="sxs-lookup"><span data-stu-id="ffe9d-428">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="ffe9d-429">Razor ページは、ページ コーディングに重点を置いたシナリオをより簡略化し、生産性を高める ASP.NET Core MVC の新たな側面です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-429">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="ffe9d-430">モデル ビュー コントローラーのアプローチを使用するチュートリアルをお探しの場合は、「[Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc)」 (ASP.NET Core MVC の概要) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-430">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="ffe9d-431">このドキュメントでは、Razor ページの概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-431">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="ffe9d-432">手順を追って説明するチュートリアルではありません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-432">It's not a step by step tutorial.</span></span> <span data-ttu-id="ffe9d-433">セクションの一部を理解できない場合は、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-433">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="ffe9d-434">ASP.NET Core の概要については、「[ASP.NET Core の概要](xref:index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-434">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ffe9d-435">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ffe9d-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffe9d-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffe9d-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffe9d-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffe9d-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffe9d-438">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ffe9d-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="ffe9d-439">Razor ページ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ffe9d-439">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffe9d-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffe9d-440">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ffe9d-441">Razor ページ プロジェクトを作成する詳細な手順については、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-441">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffe9d-442">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ffe9d-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ffe9d-443">コマンド ラインから `dotnet new webapp` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-443">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="ffe9d-444">Visual Studio for Mac から生成された *.csproj* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-444">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffe9d-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffe9d-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ffe9d-446">コマンド ラインから `dotnet new webapp` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-446">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="ffe9d-447">Razor ページ</span><span class="sxs-lookup"><span data-stu-id="ffe9d-447">Razor Pages</span></span>

<span data-ttu-id="ffe9d-448">Razor ページは *Startup.cs* で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-448">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="ffe9d-449">基本ページを検討します。<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="ffe9d-449">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="ffe9d-450">上記のコードは、コントローラーとビューを含んだ ASP.NET Core アプリで使われる [Razor ビュー ファイル](xref:tutorials/first-mvc-app/adding-view)によく似ています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-450">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="ffe9d-451">違いは、`@page` ディレクティブにあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-451">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="ffe9d-452">`@page` はファイルを MVC アクションにします。つまり、コントローラーを経由せずに要求を直接処理します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-452">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="ffe9d-453">`@page` はページで最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-453">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="ffe9d-454">`@page` はその他の Razor コンストラクトの動作に影響します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-454">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="ffe9d-455">`PageModel` クラスを使用している類似したページが、次の 2 つのファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-455">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="ffe9d-456">*Pages/Index2.cshtml* ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-456">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="ffe9d-457">*Pages/Index2.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-457">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="ffe9d-458">規則により、`PageModel` クラス ファイルは、Razor ページ ファイルと同じ名前に *.cs* が付加された名前になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-458">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="ffe9d-459">たとえば、上の Razor ページは *Pages/Index2.cshtml* になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-459">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="ffe9d-460">`PageModel` クラスを含むファイル名は、*Pages/Index2.cshtml.cs* になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-460">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="ffe9d-461">URL パスのページへの関連付けは、ファイル システム内のページの場所によって決定されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-461">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="ffe9d-462">次の表に、Razor ページ パスと一致 URL を示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-462">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="ffe9d-463">ファイル名とパス</span><span class="sxs-lookup"><span data-stu-id="ffe9d-463">File name and path</span></span>               | <span data-ttu-id="ffe9d-464">一致 URL</span><span class="sxs-lookup"><span data-stu-id="ffe9d-464">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="ffe9d-465">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-465">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="ffe9d-466">`/` または `/Index`</span><span class="sxs-lookup"><span data-stu-id="ffe9d-466">`/` or `/Index`</span></span> |
| <span data-ttu-id="ffe9d-467">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-467">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="ffe9d-468">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-468">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="ffe9d-469">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-469">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="ffe9d-470">`/Store` または `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="ffe9d-470">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="ffe9d-471">メモ:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-471">Notes:</span></span>

* <span data-ttu-id="ffe9d-472">既定では、ランタイムが *Pages* フォルダー内で Razor ページ ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-472">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="ffe9d-473">`Index` は、URL にページが含まれない場合の既定のページになります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-473">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="ffe9d-474">基本フォームを作成する</span><span class="sxs-lookup"><span data-stu-id="ffe9d-474">Write a basic form</span></span>

<span data-ttu-id="ffe9d-475">Razor ページは、アプリの構築時に Web ブラウザーで使用される一般的なパターンを実装しやすくするために設計されています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-475">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="ffe9d-476">[モデル バインド](xref:mvc/models/model-binding)、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、および HTML ヘルパーはすべて、Razor ページ クラスで定義されたプロパティで*機能します*。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-476">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="ffe9d-477">`Contact` モデルの基本的な "お問い合わせ" フォームを実装するページを考察します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-477">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="ffe9d-478">このドキュメントのサンプルでは、[Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) ファイルで `DbContext` が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-478">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="ffe9d-479">データ モデル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-479">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="ffe9d-480">db コンテキスト:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-480">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="ffe9d-481">*Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-481">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="ffe9d-482">*Pages/Create.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-482">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="ffe9d-483">規則により、`PageModel` クラスは `<PageName>Model` と呼ばれ、ページと同じ名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-483">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="ffe9d-484">`PageModel` クラスでは、ページの表示からロジックを分離できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-484">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="ffe9d-485">これは、ページに送信される要求のページ ハンドラーと、ページのレンダリングに使用されるデータを定義します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-485">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="ffe9d-486">この分離により可能になること:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-486">This separation allows:</span></span>

* <span data-ttu-id="ffe9d-487">[依存関係の挿入](xref:fundamentals/dependency-injection)によるページの依存関係の管理。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-487">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="ffe9d-488">ページを[単体テスト](xref:test/razor-pages-tests)します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-488">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="ffe9d-489">このページには、(ユーザーがフォームを投稿したときに) `POST` 要求で実行される `OnPostAsync` "*ハンドラー メソッド*" があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-489">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="ffe9d-490">任意の HTTP 動詞のハンドラー メソッドを追加できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-490">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="ffe9d-491">最も一般的なハンドラーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-491">The most common handlers are:</span></span>

* <span data-ttu-id="ffe9d-492">ページに必要な状態を初期化するための `OnGet`。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-492">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="ffe9d-493">[OnGet](#OnGet) サンプル。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-493">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="ffe9d-494">フォームの送信を処理するための `OnPost`。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-494">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="ffe9d-495">`Async` 名前付けサフィックスは省略可能ですが、非同期関数の規則でよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-495">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="ffe9d-496">上記のコードは、Razor ページでは一般的です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-496">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="ffe9d-497">コントローラーとビューを利用する ASP.NET アプリに慣れている場合:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-497">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="ffe9d-498">前の例の `OnPostAsync` コードは、一般的なコントローラー コードに似ています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-498">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="ffe9d-499">[モデル バインド](xref:mvc/models/model-binding)、[検証](xref:mvc/models/validation)、[検証](xref:mvc/models/validation) アクションの結果などのほとんどの MVC プリミティブは共有されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-499">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="ffe9d-500">上記の `OnPostAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-500">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="ffe9d-501">`OnPostAsync` の基本的な流れは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-501">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="ffe9d-502">検証エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-502">Check for validation errors.</span></span>

* <span data-ttu-id="ffe9d-503">エラーがない場合は、データを保存し、リダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-503">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="ffe9d-504">エラーがある場合は、検証メッセージとともにページをもう一度表示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-504">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="ffe9d-505">クライアント側の検証は、従来の ASP.NET Core MVC アプリケーションと同じです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-505">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="ffe9d-506">多くの場合、検証エラーはクライアントで検出され、サーバーには送信されません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-506">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="ffe9d-507">データが正常に入力されると、`OnPostAsync` ハンドラー メソッドが `RedirectToPage` ヘルパー メソッドを呼び出して `RedirectToPageResult` のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-507">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="ffe9d-508">`RedirectToPage` は、`RedirectToAction` や `RedirectToRoute` と同じような新しいアクション結果ですが、ページ用にカスタマイズされています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-508">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="ffe9d-509">上記のサンプルでは、ルート インデックス ページ (`/Index`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-509">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="ffe9d-510">`RedirectToPage` については、「[ページの URL の生成](#url_gen)」セクションで詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-510">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="ffe9d-511">送信されたフォームに検証エラー (サーバーに渡される) があると、`OnPostAsync` ハンドラー メソッドが `Page` ヘルパー メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-511">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="ffe9d-512">`Page` は `PageResult` のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-512">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="ffe9d-513">`Page` を返すのは、コントローラーのアクションが `View` を返す方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-513">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="ffe9d-514">`PageResult` はハンドラー メソッドの既定の戻り値の型です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-514">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="ffe9d-515">`void` を返すハンドラー メソッドがページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-515">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="ffe9d-516">`Customer` プロパティは `[BindProperty]` 属性を使用してモデル バインドにオプトインします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-516">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="ffe9d-517">既定では、Razor Pages はプロパティを非 `GET` 動詞とのみバインドします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-517">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="ffe9d-518">プロパティをバインドすることで、記述すべきコードの量を削減できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-518">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="ffe9d-519">同じプロパティを使用してバインドすることでコードを減らし、フィールド (`<input asp-for="Customer.Name">`) からレンダリングして入力を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-519">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="ffe9d-520">ホーム ページ (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="ffe9d-520">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="ffe9d-521">関連付けられた `PageModel` クラス (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="ffe9d-521">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="ffe9d-522">*Index.cshtml* ファイルには、各連絡先の編集リンクを作成するために次のマークアップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-522">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="ffe9d-523">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)では、`asp-route-{value}` 属性を使用して編集ページへのリンクが生成されました。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-523">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="ffe9d-524">リンクには、連絡先 ID とともにルート データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-524">The link contains route data with the contact ID.</span></span> <span data-ttu-id="ffe9d-525">たとえば、`https://localhost:5001/Edit/1` のようにします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-525">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="ffe9d-526">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-526">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="ffe9d-527">タグ ヘルパーは `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` によって有効になります</span><span class="sxs-lookup"><span data-stu-id="ffe9d-527">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="ffe9d-528">*Pages/Edit.cshtml* ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-528">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="ffe9d-529">最初の行には `@page "{id:int}"` ディレクティブが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-529">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="ffe9d-530">ルーティングの制約 `"{id:int}"` は、`int` ルート データを含むページへの要求を受け入れるようにページに指示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-530">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="ffe9d-531">ページへの要求に `int` に変換できるルート データが含まれていない場合は、ランタイムで HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-531">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="ffe9d-532">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-532">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="ffe9d-533">*Pages/Edit.cshtml.cs* ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-533">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="ffe9d-534">*Index.cshtml* ファイルには、各顧客の連絡先の削除ボタンを作成するマークアップも含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-534">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="ffe9d-535">HTML で削除ボタンがレンダリングされる場合、その `formaction` には次のパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-535">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="ffe9d-536">`asp-route-id` 属性によって指定された顧客の連絡先 ID。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-536">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="ffe9d-537">`asp-page-handler` 属性によって指定された `handler`。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-537">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="ffe9d-538">顧客の連絡先 ID `1` でレンダリングされた削除ボタンの例を示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-538">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="ffe9d-539">ボタンが選択されると、フォームの `POST` 要求がサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-539">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="ffe9d-540">慣例により、ハンドラー メソッドの名前はスキーム `OnPost[handler]Async` に従った `handler` パラメーターの値に基づいて選択されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-540">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="ffe9d-541">この例では `handler` が `delete` であるため、`OnPostDeleteAsync` ハンドラー メソッドを使用して `POST` 要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-541">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="ffe9d-542">`asp-page-handler` が `remove` などの別の値に設定されている場合、名前が `OnPostRemoveAsync` のハンドラー メソッドが選択されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-542">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="ffe9d-543">次のコードは、`OnPostDeleteAsync` ハンドラーを示しています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-543">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="ffe9d-544">`OnPostDeleteAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-544">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="ffe9d-545">クエリ文字列から `id` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-545">Accepts the `id` from the query string.</span></span> <span data-ttu-id="ffe9d-546">*Index.cshtml* ページ ディレクティブにルーティング制約 `"{id:int?}"` が含まれていた場合、`id` はルート データから取得されることがあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-546">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="ffe9d-547">`id` のルート データは `https://localhost:5001/Customers/2` のように URI で指定されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-547">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="ffe9d-548">`FindAsync` を使用してデータベースから顧客の連絡先を照会します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-548">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="ffe9d-549">顧客の連絡先が見つかった場合、その連絡先は顧客の連絡先の一覧から削除されています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-549">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="ffe9d-550">データベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-550">The database is updated.</span></span>
* <span data-ttu-id="ffe9d-551">ルート インデックス ページ (`/Index`) にリダイレクトされるように、`RedirectToPage` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-551">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="ffe9d-552">必要に応じてページのプロパティをマークする</span><span class="sxs-lookup"><span data-stu-id="ffe9d-552">Mark page properties as required</span></span>

<span data-ttu-id="ffe9d-553">`PageModel` 上のプロパティは、[必須](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)属性を使ってマークできます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-553">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="ffe9d-554">詳細については、[モデルの検証](xref:mvc/models/validation)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-554">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="ffe9d-555">OnGet ハンドラー フォールバックを使用した HEAD 要求の処理</span><span class="sxs-lookup"><span data-stu-id="ffe9d-555">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="ffe9d-556">`HEAD`HEAD 要求を使用すると、特定のリソースに対するヘッダーを取得できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-556">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="ffe9d-557">`GET` 要求とは異なり、`HEAD` 要求から応答本文は返されません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-557">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="ffe9d-558">通常、`HEAD` 要求に対して `OnHead` ハンドラーが作成され、呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-558">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="ffe9d-559">ASP.NET Core 2.1 以降では、`OnHead` ハンドラーが定義されていない場合、Razor Pages は `OnGet` ハンドラーの呼び出しにフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-559">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="ffe9d-560">この動作は、`Startup.ConfigureServices` での [SetCompatibilityVersion](xref:mvc/compatibility-version) への呼び出しによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-560">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="ffe9d-561">既定のテンプレートでは、ASP.NET Core 2.1 および 2.2 で `SetCompatibilityVersion` の呼び出しが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-561">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="ffe9d-562">`SetCompatibilityVersion` は実質的に Razor ページのオプション `AllowMappingHeadRequestsToGetHandler` を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-562">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="ffe9d-563">`SetCompatibilityVersion` とのすべての動作にオプトインするのではなく、明示的に*特定の*動作にオプトインすることもできます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-563">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="ffe9d-564">次のコードでは、`OnGet` ハンドラーに `HEAD` 要求をマップできるようにすることにオプトインしています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-564">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="ffe9d-565">XSRF/CSRF と Razor ページ</span><span class="sxs-lookup"><span data-stu-id="ffe9d-565">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="ffe9d-566">[偽造防止検証](xref:security/anti-request-forgery)のためにコードを記述する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-566">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="ffe9d-567">偽造防止トークンの生成と検証は、自動的に Razor ページに含まれます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-567">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="ffe9d-568">Razor ページでのレイアウト、パーシャル、テンプレート、およびタグ ヘルパーの使用</span><span class="sxs-lookup"><span data-stu-id="ffe9d-568">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="ffe9d-569">ページは、Razor ビュー エンジンのすべての機能で動作します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-569">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="ffe9d-570">レイアウト、パーシャル、テンプレート、タグ ヘルパー、 *_ViewStart.cshtml*、 *_ViewImports.cshtml* は、従来の Razor ビューと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-570">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="ffe9d-571">これらの機能の一部を利用してこのページをまとめてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-571">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="ffe9d-572">[レイアウト ページ](xref:mvc/views/layout)を *Pages/Shared/_Layout.cshtml* に追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-572">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="ffe9d-573">[レイアウト](xref:mvc/views/layout)は次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-573">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="ffe9d-574">(ページでレイアウトを止めない限り) 各ページのレイアウトを制御します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-574">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="ffe9d-575">JavaScript やスタイルシートなどの HTML 構造をインポートします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-575">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="ffe9d-576">詳細については、[レイアウトのページ](xref:mvc/views/layout)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-576">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="ffe9d-577">[Layout](xref:mvc/views/layout#specifying-a-layout) プロパティは *Pages/_ViewStart.cshtml* で設定されています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-577">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="ffe9d-578">レイアウトは、*Pages/Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-578">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="ffe9d-579">ページは現在のページと同じフォルダーから開始して、階層的に他のビュー (レイアウト、テンプレート、パーシャル) を検索します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-579">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="ffe9d-580">*Pages/Shared* フォルダー内のレイアウトは、*Pages* フォルダー配下の任意の Razor ページから使用できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-580">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="ffe9d-581">レイアウト ファイルは *Pages/Shared* フォルダーに入ります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-581">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="ffe9d-582">レイアウト ファイルを *Views/Shared* フォルダー内に配置**しない**ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-582">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="ffe9d-583">*Views/Shared* は MVC ビュー パターンです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-583">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="ffe9d-584">Razor ページは、パス規則ではなく、フォルダー階層に依存することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-584">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="ffe9d-585">Razor ページからのビュー検索には、*Pages* フォルダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-585">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="ffe9d-586">MVC コントローラーで使用しているレイアウト、テンプレート、およびパーシャルと、従来の Razor ビューは*機能します*。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-586">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="ffe9d-587">*Pages/_ViewImports.cshtml* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-587">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="ffe9d-588">`@namespace` はこのチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-588">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="ffe9d-589">`@addTagHelper` ディレクティブにより、[組み込みタグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/Index)が *Pages* フォルダー内のすべてのページにもたらされます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-589">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="ffe9d-590">ページで `@namespace` ディレクティブが明示的に使用されている場合:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-590">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="ffe9d-591">ディレクティブは、ページの名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-591">The directive sets the namespace for the page.</span></span> <span data-ttu-id="ffe9d-592">`@model` ディレクティブには、名前空間を含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-592">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="ffe9d-593">`@namespace` ディレクティブが *_ViewImports.cshtml* に含まれていると、指定した名前空間が `@namespace` ディレクティブをインポートするページで生成された名前空間のプレフィックスを提供します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-593">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="ffe9d-594">生成された名前空間の残りの部分 (サフィックスの部分) は、 *_ViewImports.cshtml* を含むフォルダーとページを含むフォルダー間のドットで区切られた相対パスです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-594">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="ffe9d-595">たとえば、`PageModel` クラス *Pages/Customers/Edit.cshtml.cs* は名前空間を明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-595">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="ffe9d-596">*Pages/_ViewImports.cshtml* ファイルは次の名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-596">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="ffe9d-597">*Pages/Customers/Edit.cshtml* Razor ページの生成された名前空間は、`PageModel` クラスと同じです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-597">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="ffe9d-598">`@namespace` は "*従来の Razor ビューでも機能します。* "</span><span class="sxs-lookup"><span data-stu-id="ffe9d-598">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="ffe9d-599">元の *Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-599">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="ffe9d-600">更新された *Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-600">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="ffe9d-601">[Razor ページのスタート プロジェクト](#rpvs17)には、クライアント側の検証をフックする *Pages/_ValidationScriptsPartial.cshtml* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-601">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="ffe9d-602">部分ビューの詳細については、「<xref:mvc/views/partial>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-602">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="ffe9d-603">ページの URL の生成</span><span class="sxs-lookup"><span data-stu-id="ffe9d-603">URL generation for Pages</span></span>

<span data-ttu-id="ffe9d-604">上に示した `Create` ページでは、`RedirectToPage` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-604">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="ffe9d-605">アプリには次のファイル/フォルダー構造があります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-605">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="ffe9d-606">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-606">*/Pages*</span></span>

  * <span data-ttu-id="ffe9d-607">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-607">*Index.cshtml*</span></span>
  * <span data-ttu-id="ffe9d-608">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-608">*/Customers*</span></span>

    * <span data-ttu-id="ffe9d-609">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-609">*Create.cshtml*</span></span>
    * <span data-ttu-id="ffe9d-610">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-610">*Edit.cshtml*</span></span>
    * <span data-ttu-id="ffe9d-611">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-611">*Index.cshtml*</span></span>

<span data-ttu-id="ffe9d-612">成功すると、*Pages/Customers/Create.cshtml* ページと *Pages/Customers/Edit.cshtml* ページが *Pages/Index.cshtml* にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-612">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="ffe9d-613">文字列 `/Index` は前のページにアクセスするための URI の一部です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-613">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="ffe9d-614">文字列 `/Index` は、*Pages/Index.cshtml* ページへの URI を生成するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-614">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="ffe9d-615">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-615">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="ffe9d-616">ページ名は、先頭の `/` を含む、ルート */Pages* フォルダーからページへのパスです (たとえば `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-616">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="ffe9d-617">先述の URL 生成サンプルでは、URL のハードコーディングに関する拡張オプションと機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-617">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="ffe9d-618">URL の生成は[ルーティング](xref:mvc/controllers/routing)を使用し、ターゲット パスで定義されたルート方法に従って、パラメーターの生成とエンコードができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-618">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="ffe9d-619">ページの URL 生成は、相対名をサポートします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-619">URL generation for pages supports relative names.</span></span> <span data-ttu-id="ffe9d-620">次の表に、*Pages/Customers/Create.cshtml* の異なる `RedirectToPage` パラメーターで選択されたインデックス ページを示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-620">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="ffe9d-621">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="ffe9d-621">RedirectToPage(x)</span></span>| <span data-ttu-id="ffe9d-622">ページ</span><span class="sxs-lookup"><span data-stu-id="ffe9d-622">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="ffe9d-623">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="ffe9d-623">RedirectToPage("/Index")</span></span> | <span data-ttu-id="ffe9d-624">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-624">*Pages/Index*</span></span> |
| <span data-ttu-id="ffe9d-625">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="ffe9d-625">RedirectToPage("./Index");</span></span> | <span data-ttu-id="ffe9d-626">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-626">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="ffe9d-627">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="ffe9d-627">RedirectToPage("../Index")</span></span> | <span data-ttu-id="ffe9d-628">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-628">*Pages/Index*</span></span> |
| <span data-ttu-id="ffe9d-629">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="ffe9d-629">RedirectToPage("Index")</span></span>  | <span data-ttu-id="ffe9d-630">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="ffe9d-630">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="ffe9d-631">`RedirectToPage("Index")`、`RedirectToPage("./Index")`、および `RedirectToPage("../Index")` は*相対名*です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="ffe9d-632">`RedirectToPage` パラメーターは現在のページのパスと*組み合わされて*、ターゲット ページの名前を計算します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-632">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="ffe9d-633">相対名のリンクは、複雑な構造を持つサイトを構築する際に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-633">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="ffe9d-634">相対名を使用してフォルダー内のページ間をリンクする場合、そのフォルダー名を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-634">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="ffe9d-635">すべてのリンクは引き続き機能します (リンクにはフォルダー名が含まれていないため)。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-635">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="ffe9d-636">別の [[区分]](xref:mvc/controllers/areas) のページにリダイレクトするには、その区分を指定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-636">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="ffe9d-637">詳細については、「<xref:mvc/controllers/areas>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-637">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="ffe9d-638">ViewData 属性</span><span class="sxs-lookup"><span data-stu-id="ffe9d-638">ViewData attribute</span></span>

<span data-ttu-id="ffe9d-639">データは [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) とのページに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-639">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="ffe9d-640">`[ViewData]` 属性を持つコントローラーまたは Razor ページのモデルのプロパティの値は、[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) に格納してそこから読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-640">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="ffe9d-641">次の例では、`AboutModel` に `[ViewData]` でマークされた `Title` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-641">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="ffe9d-642">`Title` プロパティは、[About] ページのタイトルに設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-642">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="ffe9d-643">[About] ページでは、モデル プロパティとして `Title` プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-643">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="ffe9d-644">レイアウトでは、タイトルは ViewData ディクショナリから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-644">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="ffe9d-645">TempData</span><span class="sxs-lookup"><span data-stu-id="ffe9d-645">TempData</span></span>

<span data-ttu-id="ffe9d-646">ASP.NET Core は [コントローラー](/dotnet/api/microsoft.aspnetcore.mvc.controller)上で [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-646">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="ffe9d-647">このプロパティは、読み取られるまでデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-647">This property stores data until it's read.</span></span> <span data-ttu-id="ffe9d-648">`Keep` メソッドと `Peek` メソッドは、削除せずにデータを確認するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-648">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="ffe9d-649">`TempData` は、複数の要求にデータが必要な場合のリダイレクトに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-649">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="ffe9d-650">次のコードは、`TempData` を使用して `Message` の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-650">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="ffe9d-651">*Pages/Customers/Index.cshtml* ファイル内の次のマークアップは、`TempData` を使用して `Message` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-651">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="ffe9d-652">*Pages/Customers/Index.cshtml.cs* ページは、`[TempData]` 属性を `Message` プロパティに適用します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-652">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="ffe9d-653">詳細については、「[TempData](xref:fundamentals/app-state#tempdata)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-653">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="ffe9d-654">ページあたり複数のハンドラー</span><span class="sxs-lookup"><span data-stu-id="ffe9d-654">Multiple handlers per page</span></span>

<span data-ttu-id="ffe9d-655">次のページでは、`asp-page-handler` タグ ヘルパーを使用して 2 つのハンドラーにマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-655">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="ffe9d-656">前の例のフォームには、それぞれが `FormActionTagHelper` を使用して異なる URL に送信する 2 つの送信ボタンがあります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-656">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="ffe9d-657">`asp-page-handler` 属性は、`asp-page` のコンパニオンです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-657">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="ffe9d-658">`asp-page-handler` はページごとに定義されている各ハンドラー メソッドに送信する URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-658">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="ffe9d-659">サンプルは現在のページにリンクしているため、`asp-page` は指定されません。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-659">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="ffe9d-660">ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-660">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="ffe9d-661">上記のコードは、*名前付きハンドラー メソッド*を使用しています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-661">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="ffe9d-662">名前付きハンドラー メソッドは、名前の `On<HTTP Verb>` の後および `Async` の前 (ある場合) のテキストを取得して作成されます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-662">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="ffe9d-663">前の例では、ページ メソッドは OnPost**JoinList**Async と OnPost**JoinListUC**Async です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-663">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="ffe9d-664">*OnPost* と *Async* を削除すると、ハンドラー名は `JoinList` と `JoinListUC` になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-664">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="ffe9d-665">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-665">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="ffe9d-666">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-666">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="ffe9d-667">カスタム ルート</span><span class="sxs-lookup"><span data-stu-id="ffe9d-667">Custom routes</span></span>

<span data-ttu-id="ffe9d-668">`@page` ディレクティブを次に使用します:</span><span class="sxs-lookup"><span data-stu-id="ffe9d-668">Use the `@page` directive to:</span></span>

* <span data-ttu-id="ffe9d-669">カスタム ルートをページに指定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-669">Specify a custom route to a page.</span></span> <span data-ttu-id="ffe9d-670">たとえば、[バージョン情報] ページへのルートを `@page "/Some/Other/Path"` を使用して `/Some/Other/Path` に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-670">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="ffe9d-671">ページの既定のルートにセグメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-671">Append segments to a page's default route.</span></span> <span data-ttu-id="ffe9d-672">たとえば、"item" セグメントを `@page "item"` を使用してページの既定のルートに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-672">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="ffe9d-673">ページの既定のルートにパラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-673">Append parameters to a page's default route.</span></span> <span data-ttu-id="ffe9d-674">たとえば、`@page "{id}"` を含むページに ID パラメーター `id` を必須とすることができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-674">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="ffe9d-675">パスの先頭のチルダ (`~`) によって指定されたルートの相対パスがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-675">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="ffe9d-676">たとえば、`@page "~/Some/Other/Path"` は `@page "/Some/Other/Path"` と同じです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-676">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="ffe9d-677">URL 内のクエリ文字列 `?handler=JoinList` が気に入らない場合は、ルートを変更して URL のパス部分にハンドラー名を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-677">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="ffe9d-678">`@page` ディレクティブの後に二重引用符で囲んだルート テンプレートを追加して、ルートをカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-678">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="ffe9d-679">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-679">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="ffe9d-680">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-680">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="ffe9d-681">`handler` の後の `?` は、ルート パラメーターが省略可能なことを意味します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-681">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="ffe9d-682">構成と設定</span><span class="sxs-lookup"><span data-stu-id="ffe9d-682">Configuration and settings</span></span>

<span data-ttu-id="ffe9d-683">高度なオプションを構成するには、MVC ビルダーで拡張メソッド `AddRazorPagesOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-683">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="ffe9d-684">現在は、`RazorPagesOptions` を使用してページのルート ディレクトリを設定したり、ページのアプリケーション モデルの規則を追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-684">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="ffe9d-685">将来、この方法でより多くの機能拡張を可能にしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-685">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="ffe9d-686">ビューをプリコンパイルするには、[Razor ビューのコンパイル](xref:mvc/views/view-compilation)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-686">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="ffe9d-687">[サンプル コードをダウンロードまたは表示します](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample)。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-687">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="ffe9d-688">この概要に基づく、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するページをご覧ください</span><span class="sxs-lookup"><span data-stu-id="ffe9d-688">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="ffe9d-689">Razor Pages をコンテンツのルートに指定する</span><span class="sxs-lookup"><span data-stu-id="ffe9d-689">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="ffe9d-690">Razor Pages のルートは既定で */Pages* ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-690">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="ffe9d-691">[WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) を [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) に追加して、Razor Pages をアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) に置くように指定します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-691">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="ffe9d-692">Razor Pages をカスタム ルート ディレクトリに指定する</span><span class="sxs-lookup"><span data-stu-id="ffe9d-692">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="ffe9d-693">(相対パスを指定して) Razor Pages をアプリのカスタム ルート ディレクトリに指定するには、[WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) を [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) に追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe9d-693">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="ffe9d-694">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ffe9d-694">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
