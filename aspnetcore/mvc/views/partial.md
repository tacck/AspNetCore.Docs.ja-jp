---
title: ASP.NET Core の部分ビュー
author: ardalis
description: 部分ビューを使用して大規模なマークアップ ファイルを分割し、ASP.NET Core アプリの Web ページ間で共通するマークアップの重複を減らす方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: bb75d8b7d78c82c9c32605ac645b8895fdfb78e3
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393653"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="32eaa-103">ASP.NET Core の部分ビュー</span><span class="sxs-lookup"><span data-stu-id="32eaa-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="32eaa-104">作成者: [Steve Smith](https://ardalis.com/)、[Maher JENDOUBI](https://twitter.com/maherjend)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="32eaa-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="32eaa-105">部分ビューは、 [Razor](xref:mvc/views/razor) *.cshtml* [`@page`](xref:mvc/views/razor#page) 別のマークアップファイルの表示出力*内で*HTML 出力をレンダリングするディレクティブのないマークアップファイル (cshtml) です。</span><span class="sxs-lookup"><span data-stu-id="32eaa-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="32eaa-106">*部分ビュー*という用語は、マークアップファイルが*ビュー*と呼ばれる MVC アプリを開発する場合や、 Razor マークアップファイルが*ページ*と呼ばれるページアプリを開発する場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="32eaa-107">このトピックでは、一般的に MVC ビューと Razor ページページを *マークアップファイル*と呼びます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="32eaa-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="32eaa-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="32eaa-109">部分ビューを使用する状況</span><span class="sxs-lookup"><span data-stu-id="32eaa-109">When to use partial views</span></span>

<span data-ttu-id="32eaa-110">部分ビューは、次の場合に効果的な方法です。</span><span class="sxs-lookup"><span data-stu-id="32eaa-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="32eaa-111">大規模なマークアップ ファイルをより小さなコンポーネントに分割します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="32eaa-112">複数の論理パーツで構成された大規模かつ複雑なマークアップ ファイルでは、各パーツが部分ビューに分かれた状態で操作すると便利です。</span><span class="sxs-lookup"><span data-stu-id="32eaa-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="32eaa-113">全体のページ構造と部分ビューへの参照がマークアップのみに含まれるため、マークアップ ファイルのコードは管理性に優れています。</span><span class="sxs-lookup"><span data-stu-id="32eaa-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="32eaa-114">マークアップ ファイル間で共通するマークアップ コンテンツの重複を減らします。</span><span class="sxs-lookup"><span data-stu-id="32eaa-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="32eaa-115">マークアップ ファイル間で同じマークアップ要素が使用されている場合、1 つの部分ビュー ファイルとなり、部分ビューによってマークアップ コンテンツの重複が除去されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="32eaa-116">部分ビュー内でマークアップが変更された場合、その部分ビューを使用しているマークアップ ファイルの出力表示が更新されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="32eaa-117">共通するレイアウト要素を維持するために、部分ビューを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="32eaa-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="32eaa-118">共通するレイアウト要素は、[_Layout.cshtml](xref:mvc/views/layout) ファイルで指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="32eaa-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="32eaa-119">マークアップを表示するために、複雑な表示ロジックやコード実行が必要になる場合は、部分ビューを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="32eaa-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="32eaa-120">部分ビューの代わりに、[ビュー コンポーネント](xref:mvc/views/view-components)を使用してください。</span><span class="sxs-lookup"><span data-stu-id="32eaa-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="32eaa-121">部分ビューを宣言する</span><span class="sxs-lookup"><span data-stu-id="32eaa-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="32eaa-122">部分ビューは、 *.cshtml* [`@page`](xref:mvc/views/razor#page) *Views*フォルダー (MVC) または*pages*フォルダー ( Razor ページ) 内でディレクティブが保持されていない、cshtml マークアップファイルです。</span><span class="sxs-lookup"><span data-stu-id="32eaa-122">A partial view is a *.cshtml* markup file without an [`@page`](xref:mvc/views/razor#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="32eaa-123">ASP.NET Core MVC では、コントローラーの <xref:Microsoft.AspNetCore.Mvc.ViewResult> が、ビューまたは部分ビューのどちらかを返すことができます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="32eaa-124">Razorページでは、は <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> オブジェクトとして表される部分ビューを返すことができ <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> ます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-124">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="32eaa-125">部分ビューの参照と表示については、「[部分ビューを参照する](#reference-a-partial-view)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="32eaa-126">MVC ビューやページ レンダリングとは異なり、部分ビューは *_ViewStart.cshtml* を実行しません。</span><span class="sxs-lookup"><span data-stu-id="32eaa-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="32eaa-127">*_ViewStart.cshtml* の詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="32eaa-127">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="32eaa-128">部分ビューのファイル名は、多くの場合アンダースコア (`_`) から始まります。</span><span class="sxs-lookup"><span data-stu-id="32eaa-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="32eaa-129">この名前付け規則は必須ではありませんが、ビューおよびページと部分ビューを視覚的に区別するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="32eaa-130">部分ビューは、*Views* フォルダー内で保持される *.cshtml* マークアップ ファイルです。</span><span class="sxs-lookup"><span data-stu-id="32eaa-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="32eaa-131">コントローラーの <xref:Microsoft.AspNetCore.Mvc.ViewResult> は、ビューまたは部分ビューのどちらかを返すことができます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="32eaa-132">部分ビューの参照と表示については、「[部分ビューを参照する](#reference-a-partial-view)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="32eaa-133">MVC ビューのレンダリングとは異なり、部分ビューは *_ViewStart.cshtml* を実行しません。</span><span class="sxs-lookup"><span data-stu-id="32eaa-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="32eaa-134">*_ViewStart.cshtml* の詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="32eaa-134">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="32eaa-135">部分ビューのファイル名は、多くの場合アンダースコア (`_`) から始まります。</span><span class="sxs-lookup"><span data-stu-id="32eaa-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="32eaa-136">この名前付け規則は必須ではありませんが、ビューと部分ビューを視覚的に区別するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="32eaa-137">部分ビューを参照する</span><span class="sxs-lookup"><span data-stu-id="32eaa-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a><span data-ttu-id="32eaa-138">PageModel ページで部分ビューを使用する Razor</span><span class="sxs-lookup"><span data-stu-id="32eaa-138">Use a partial view in a Razor Pages PageModel</span></span>

<span data-ttu-id="32eaa-139">ASP.NET Core 2.0 または2.1 で、次のハンドラーメソッドは、 \* \_ authorpartialrp. cshtml\*部分ビューを応答にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="32eaa-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="32eaa-140">ASP.NET Core 2.2 以降では、別の方法としてハンドラー メソッドによって、<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> メソッドを呼び出して `PartialViewResult` オブジェクトを生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="32eaa-141">マークアップ ファイルで部分ビューを使用する</span><span class="sxs-lookup"><span data-stu-id="32eaa-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="32eaa-142">マークアップ ファイル内で、部分ビューを参照する方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="32eaa-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="32eaa-143">アプリでは、次の非同期レンダリング手法のいずれかを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="32eaa-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="32eaa-144">部分タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="32eaa-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="32eaa-145">非同期の HTML ヘルパー</span><span class="sxs-lookup"><span data-stu-id="32eaa-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="32eaa-146">マークアップ ファイル内で、部分ビューを参照するには、次の 2 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="32eaa-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="32eaa-147">非同期の HTML ヘルパー</span><span class="sxs-lookup"><span data-stu-id="32eaa-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="32eaa-148">同期の HTML ヘルパー</span><span class="sxs-lookup"><span data-stu-id="32eaa-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="32eaa-149">アプリでは、[非同期の HTML ヘルパー](#asynchronous-html-helper)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="32eaa-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="32eaa-150">部分タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="32eaa-150">Partial Tag Helper</span></span>

<span data-ttu-id="32eaa-151">[部分タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)では、ASP.NET Core 2.1 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="32eaa-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="32eaa-152">部分タグ ヘルパーでは、非同期でコンテンツをレンダリングして、HTML のような構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="32eaa-153">ファイル拡張子がある場合、タグ ヘルパーは、部分ビューを呼び出すマークアップ ファイルと同じフォルダー内に必ず配置されている部分ビューを参照します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="32eaa-154">次の例では、アプリ ルートから部分ビューを参照しています。</span><span class="sxs-lookup"><span data-stu-id="32eaa-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="32eaa-155">チルダとスラッシュ (`~/`) またはスラッシュ (`/`) から始まるパスは、次のようにアプリ ルートを参照します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="32eaa-156">**Razor トピック**</span><span class="sxs-lookup"><span data-stu-id="32eaa-156">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="32eaa-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="32eaa-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="32eaa-158">次の例では、相対パスを使って部分ビューを参照しています。</span><span class="sxs-lookup"><span data-stu-id="32eaa-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="32eaa-159">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="32eaa-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="32eaa-160">非同期の HTML ヘルパー</span><span class="sxs-lookup"><span data-stu-id="32eaa-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="32eaa-161">HTML ヘルパーを使用している場合、ベスト プラクティスは <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*> を使用することです。</span><span class="sxs-lookup"><span data-stu-id="32eaa-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="32eaa-162">`PartialAsync` は、<xref:System.Threading.Tasks.Task%601> でラップされた <xref:Microsoft.AspNetCore.Html.IHtmlContent> 型を返します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="32eaa-163">待機中の呼び出しの前に `@` 文字を付与することで、メソッドが参照されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="32eaa-164">ファイル拡張子がある場合、HTML ヘルパーは、部分ビューを呼び出すマークアップ ファイルと同じフォルダー内に必ず配置されている部分ビューを参照します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="32eaa-165">次の例では、アプリ ルートから部分ビューを参照しています。</span><span class="sxs-lookup"><span data-stu-id="32eaa-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="32eaa-166">チルダとスラッシュ (`~/`) またはスラッシュ (`/`) から始まるパスは、次のようにアプリ ルートを参照します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="32eaa-167">**Razor トピック**</span><span class="sxs-lookup"><span data-stu-id="32eaa-167">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="32eaa-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="32eaa-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="32eaa-169">次の例では、相対パスを使って部分ビューを参照しています。</span><span class="sxs-lookup"><span data-stu-id="32eaa-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="32eaa-170">代わりに、<xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*> を使って部分ビューをレンダリングすることもできます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="32eaa-171">このメソッドは <xref:Microsoft.AspNetCore.Html.IHtmlContent> を返しません。</span><span class="sxs-lookup"><span data-stu-id="32eaa-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="32eaa-172">レンダリングされた出力を直接応答にストリーミングします。</span><span class="sxs-lookup"><span data-stu-id="32eaa-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="32eaa-173">メソッドは結果を返さないため、コードブロック内で呼び出す必要があり Razor ます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-173">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="32eaa-174">`RenderPartialAsync` は表示されるコンテンツをストリーム配信するため、一部のシナリオではより高いパフォーマンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="32eaa-175">パフォーマンスが重要な場合には、両方の手法を使用してページのベンチマークを実行し、より迅速に応答を生成する手法を採用します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="32eaa-176">同期の HTML ヘルパー</span><span class="sxs-lookup"><span data-stu-id="32eaa-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="32eaa-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> と <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> はそれぞれ、`PartialAsync` と `RenderPartialAsync` の同期に相当します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="32eaa-178">デッドロックが発生するシナリオがあるため、同期に相当する機能はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="32eaa-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="32eaa-179">同期メソッドは、将来のリリースでは削除対象になります。</span><span class="sxs-lookup"><span data-stu-id="32eaa-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="32eaa-180">コードを実行する必要がある場合は、部分ビューの代わりに[ビュー コンポーネント](xref:mvc/views/view-components)を使用してください。</span><span class="sxs-lookup"><span data-stu-id="32eaa-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="32eaa-181">`Partial` または `RenderPartial` を呼び出すと、Visual Studio アナライザーの警告が発生します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="32eaa-182">たとえば、`Partial` を指定すると、次の警告メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="32eaa-183">IHtmlHelper.Partial を使用すると、アプリケーションのデッドロックが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="32eaa-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="32eaa-184">&lt;部分&gt;タグ ヘルパーまたは IHtmlHelper.PartialAsync を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="32eaa-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="32eaa-185">`@Html.Partial` の呼び出しを、`@await Html.PartialAsync` または[部分タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="32eaa-186">部分タグ ヘルパーの移行の詳細については、「[HTML ヘルパーから移行する](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="32eaa-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="32eaa-187">部分ビューの検出</span><span class="sxs-lookup"><span data-stu-id="32eaa-187">Partial view discovery</span></span>

<span data-ttu-id="32eaa-188">部分ビューがファイル拡張子を指定せずに名前で参照された場合、所定の順序で次の場所が検索されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="32eaa-189">**Razor トピック**</span><span class="sxs-lookup"><span data-stu-id="32eaa-189">**Razor Pages**</span></span>

1. <span data-ttu-id="32eaa-190">現在実行中のページのフォルダー</span><span class="sxs-lookup"><span data-stu-id="32eaa-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="32eaa-191">ページのフォルダーの上にあるディレクトリ グラフ</span><span class="sxs-lookup"><span data-stu-id="32eaa-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="32eaa-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="32eaa-192">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="32eaa-193">部分ビューの検索には、次の規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="32eaa-194">部分ビューが異なるフォルダー内にある場合は、同じファイル名の別の部分ビューが許可されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="32eaa-195">ファイル拡張子を指定せずに部分ビューを名前で参照しており、かつ、部分ビューが呼び出し元のフォルダーと *Shared* フォルダーの両方に存在する場合、呼び出し元のフォルダーにある部分ビューが、部分ビューとして機能します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="32eaa-196">部分ビューが呼び出し元のフォルダーに存在しない場合、部分ビューは *Shared* フォルダーから提供されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="32eaa-197">*Shared* フォルダーの部分ビューは、"*共有の部分ビュー*" または "*既定の部分ビュー*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="32eaa-198">部分ビューは、 *chained* &mdash; 呼び出しによって循環参照が形成されていない場合に、部分ビューを連結して別の部分ビューを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="32eaa-199">相対パスは常に、ファイルのルートや親ではなく、現在のファイルを基準とします。</span><span class="sxs-lookup"><span data-stu-id="32eaa-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="32eaa-200">[Razor](xref:mvc/views/razor) `section` 部分ビューで定義されたは、親マークアップファイルからは見えません。</span><span class="sxs-lookup"><span data-stu-id="32eaa-200">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="32eaa-201">`section` は定義されている部分ビューにのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="32eaa-202">部分ビューからデータにアクセスする</span><span class="sxs-lookup"><span data-stu-id="32eaa-202">Access data from partial views</span></span>

<span data-ttu-id="32eaa-203">部分ビューがインスタンス化されると、親の `ViewData` ディクショナリの*コピー*を取得します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="32eaa-204">親ビュー内のデータに対して行われた更新は、親ビューでは保持されません。</span><span class="sxs-lookup"><span data-stu-id="32eaa-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="32eaa-205">部分ビューで変更された `ViewData` は、部分ビューから返されるときに失われます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="32eaa-206">次の例に、[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) のインスタンスを部分ビューに渡す方法を示します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="32eaa-207">部分ビューにモデルを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="32eaa-208">モデルは、カスタム オブジェクトでもかまいません。</span><span class="sxs-lookup"><span data-stu-id="32eaa-208">The model can be a custom object.</span></span> <span data-ttu-id="32eaa-209">`PartialAsync` (呼び出し元にコンテンツのブロックを表示する) または `RenderPartialAsync` (コンテンツを出力にストリーム配信する) を使って、モデルを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="32eaa-210">**Razor トピック**</span><span class="sxs-lookup"><span data-stu-id="32eaa-210">**Razor Pages**</span></span>

<span data-ttu-id="32eaa-211">サンプル アプリの次のマークアップは、*Pages/ArticlesRP/ReadRP.cshtml* ページが元になっています。</span><span class="sxs-lookup"><span data-stu-id="32eaa-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="32eaa-212">ページには、2 つの部分ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="32eaa-212">The page contains two partial views.</span></span> <span data-ttu-id="32eaa-213">2 番目の部分ビューは、モデルと `ViewData` を部分ビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="32eaa-214">`ViewDataDictionary` のコンストラクター オーバーロードは、既存の `ViewData` ディクショナリを維持したまま、新しい `ViewData` ディクショナリを渡すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="32eaa-215">*Pages/Shared/_AuthorPartialRP.cshtml* は、*ReadRP.cshtml* マークアップ ファイルで参照される最初の部分ビューです。</span><span class="sxs-lookup"><span data-stu-id="32eaa-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="32eaa-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* は、*ReadRP.cshtml* マークアップ ファイルで参照される 2 番目の部分ビューです。</span><span class="sxs-lookup"><span data-stu-id="32eaa-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="32eaa-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="32eaa-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="32eaa-218">サンプル アプリの以下のマークアップは、*Views/Articles/Read.cshtml* ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="32eaa-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="32eaa-219">ビューには、2 つの部分ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="32eaa-219">The view contains two partial views.</span></span> <span data-ttu-id="32eaa-220">2 番目の部分ビューは、モデルと `ViewData` を部分ビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="32eaa-221">`ViewDataDictionary` のコンストラクター オーバーロードは、既存の `ViewData` ディクショナリを維持したまま、新しい `ViewData` ディクショナリを渡すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="32eaa-222">*Views/Shared/_AuthorPartial.cshtml* は、*Read.cshtml* マークアップ ファイルで参照される最初の部分ビューです。</span><span class="sxs-lookup"><span data-stu-id="32eaa-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="32eaa-223">*Views/Articles/_ArticleSection.cshtml* は、*Read.cshtml* マークアップ ファイルで参照される 2 番目の部分ビューです。</span><span class="sxs-lookup"><span data-stu-id="32eaa-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="32eaa-224">この部分は実行時に、親のマークアップファイルの出力表示にレンダリングされます。親ビュー自体は共有されている *_Layout.cshtml* 内にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="32eaa-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="32eaa-225">最初の部分ビューは、記事の作成者の名前と発行日を表示します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="32eaa-226">アブラハム リンカーン</span><span class="sxs-lookup"><span data-stu-id="32eaa-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="32eaa-227">この部分ビューは&lt;共有されている部分ビューのファイル パス&gt;が元になっている。</span><span class="sxs-lookup"><span data-stu-id="32eaa-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="32eaa-228">1863 年 11 月 19 日 12 時 00 分 00 秒</span><span class="sxs-lookup"><span data-stu-id="32eaa-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="32eaa-229">2 番目の部分ビューは、次に示す記事のセクションを表示します。</span><span class="sxs-lookup"><span data-stu-id="32eaa-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="32eaa-230">セクション 1 のインデックス: 0</span><span class="sxs-lookup"><span data-stu-id="32eaa-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="32eaa-231">87 年前...</span><span class="sxs-lookup"><span data-stu-id="32eaa-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="32eaa-232">セクション 2 のインデックス: 1</span><span class="sxs-lookup"><span data-stu-id="32eaa-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="32eaa-233">大きな内紛の渦中で、試した...</span><span class="sxs-lookup"><span data-stu-id="32eaa-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="32eaa-234">セクション 3 のインデックス: 2</span><span class="sxs-lookup"><span data-stu-id="32eaa-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="32eaa-235">しかし、広義では、我々が献身することはできない...</span><span class="sxs-lookup"><span data-stu-id="32eaa-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="32eaa-236">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="32eaa-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
