---
title: ASP.NET Core Blazor レイアウト
author: guardrex
description: Blazor アプリの再利用可能なレイアウト コンポーネントを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 09cca9c4af23c35fdbc2ee92169913c960b0a68d
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424331"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="90a64-103">ASP.NET Core Blazor レイアウト</span><span class="sxs-lookup"><span data-stu-id="90a64-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="90a64-104">作成者: [Rainer Stropek](https://www.timecockpit.com)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="90a64-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="90a64-105">メニュー、著作権メッセージ、会社のロゴなどの一部のアプリ要素は、通常、アプリの全体のレイアウトの一部であり、アプリのすべてのコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="90a64-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="90a64-106">これらの要素のコードをアプリのすべてのコンポーネントにコピーするのは、効率的な方法ではありません。</span><span class="sxs-lookup"><span data-stu-id="90a64-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="90a64-107">要素の 1 つに更新が必要になるたびに、すべてのコンポーネントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90a64-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="90a64-108">このような複製を維持することは困難であり、時間の経過と共にコンテンツの一貫性が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="90a64-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="90a64-109">*レイアウト*によって、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="90a64-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="90a64-110">技術的に、レイアウトはもう 1 つのコンポーネントにすぎません。</span><span class="sxs-lookup"><span data-stu-id="90a64-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="90a64-111">レイアウトは Razor テンプレートまたは C# コードで定義され、[データ バインディング](xref:blazor/data-binding)、[依存関係の挿入](xref:blazor/dependency-injection)、その他のコンポーネント シナリオを使用できます。</span><span class="sxs-lookup"><span data-stu-id="90a64-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="90a64-112">*コンポーネント*を*レイアウト*に変えるには、コンポーネントが:</span><span class="sxs-lookup"><span data-stu-id="90a64-112">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="90a64-113">レイアウト内のレンダリングされるコンテンツの `Body` プロパティを定義する `LayoutComponentBase` から継承している。</span><span class="sxs-lookup"><span data-stu-id="90a64-113">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="90a64-114">Razor 構文 `@Body` を使用して、コンテンツがレンダリングされるレイアウト マークアップ内の場所を指定している。</span><span class="sxs-lookup"><span data-stu-id="90a64-114">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="90a64-115">次のコード サンプルに、レイアウト コンポーネント *MainLayout.razor* の Razor テンプレートを示します。</span><span class="sxs-lookup"><span data-stu-id="90a64-115">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="90a64-116">レイアウトは `LayoutComponentBase` を継承し、ナビゲーション バーとフッターの間に `@Body` を設定します。</span><span class="sxs-lookup"><span data-stu-id="90a64-116">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="90a64-117">Blazor アプリ テンプレートのいずれかに基づくアプリでは、`MainLayout` コンポーネント (*MainLayout.razor*) は、アプリの*共有*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="90a64-117">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="90a64-118">既定のレイアウト</span><span class="sxs-lookup"><span data-stu-id="90a64-118">Default layout</span></span>

<span data-ttu-id="90a64-119">アプリの *App.razor* ファイル内の `Router` コンポーネントに、既定のアプリ レイアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="90a64-119">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="90a64-120">既定の Blazor テンプレートによって提供される次の `Router` コンポーネントは、既定のレイアウトを `MainLayout` コンポーネントに設定します。</span><span class="sxs-lookup"><span data-stu-id="90a64-120">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="90a64-121">`NotFound` コンテンツの既定のレイアウトを指定するには、`NotFound` コンテンツの `LayoutView` を指定します。</span><span class="sxs-lookup"><span data-stu-id="90a64-121">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="90a64-122">`Router` コンポーネントの詳細については、「<xref:blazor/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90a64-122">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="90a64-123">ルーターでレイアウトを既定のレイアウトとして指定することは、コンポーネントごとまたはフォルダーごとにオーバーライドできるため、便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="90a64-123">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="90a64-124">ルーターを使用してアプリの既定のレイアウトを設定することは、最も一般的な技法であるため、お勧めします。</span><span class="sxs-lookup"><span data-stu-id="90a64-124">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="90a64-125">コンポーネントにレイアウトを指定する</span><span class="sxs-lookup"><span data-stu-id="90a64-125">Specify a layout in a component</span></span>

<span data-ttu-id="90a64-126">コンポーネントにレイアウトを適用するには、Razor ディレクティブ `@layout` を使用します。</span><span class="sxs-lookup"><span data-stu-id="90a64-126">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="90a64-127">コンパイラでは `@layout` を `LayoutAttribute` に変換します。これはコンポーネント クラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="90a64-127">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="90a64-128">次の `MasterList` コンポーネントのコンテンツは、`@Body` の位置にある `MasterLayout` に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="90a64-128">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="90a64-129">コンポーネントに直接レイアウトを指定すると、ルーターに設定された*既定のレイアウトまたは *_Imports.razor* からインポートされた `@layout` ディレクティブ*がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90a64-129">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="90a64-130">一元的なレイアウトの選択</span><span class="sxs-lookup"><span data-stu-id="90a64-130">Centralized layout selection</span></span>

<span data-ttu-id="90a64-131">アプリのすべてのフォルダーには、必要に応じて、 *_Imports.razor* という名前のテンプレート ファイルを格納できます。</span><span class="sxs-lookup"><span data-stu-id="90a64-131">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="90a64-132">コンパイラにより、インポート ファイルに指定されたディレクティブが、同じフォルダー内とそのすべてのサブフォルダー内で再帰的にすべての Razor テンプレートに含まれます。</span><span class="sxs-lookup"><span data-stu-id="90a64-132">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="90a64-133">そのため、`@layout MyCoolLayout` を含む *_Imports.razor* ファイルにより、フォルダー内のすべてのコンポーネントで `MyCoolLayout` が確実に使用されます。</span><span class="sxs-lookup"><span data-stu-id="90a64-133">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="90a64-134">フォルダーおよびサブフォルダー内のすべての *.razor* ファイルに `@layout MyCoolLayout` を繰り返し追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="90a64-134">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="90a64-135">`@using` ディレクティブは、同じようにコンポーネントにも適用されます。</span><span class="sxs-lookup"><span data-stu-id="90a64-135">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="90a64-136">次の *_Imports.razor* ファイルでは、次のものをインポートします。</span><span class="sxs-lookup"><span data-stu-id="90a64-136">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="90a64-137">`MyCoolLayout`。</span><span class="sxs-lookup"><span data-stu-id="90a64-137">`MyCoolLayout`.</span></span>
* <span data-ttu-id="90a64-138">同じフォルダーおよびサブフォルダー内のすべての Razor コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="90a64-138">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="90a64-139">`BlazorApp1.Data` 名前空間。</span><span class="sxs-lookup"><span data-stu-id="90a64-139">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="90a64-140">*_Imports.razor* ファイルは、[Razor ビューおよびページに対する _ViewImports.cshtml ファイル](xref:mvc/views/layout#importing-shared-directives)に似ていますが、Razor コンポーネント ファイルに限定して適用されます。</span><span class="sxs-lookup"><span data-stu-id="90a64-140">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="90a64-141">*_Imports.razor* にレイアウトを指定すると、ルーターの*既定のレイアウト*として指定されたレイアウトがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90a64-141">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="90a64-142">入れ子になったレイアウト</span><span class="sxs-lookup"><span data-stu-id="90a64-142">Nested layouts</span></span>

<span data-ttu-id="90a64-143">アプリは、入れ子になったレイアウトで構成できます。</span><span class="sxs-lookup"><span data-stu-id="90a64-143">Apps can consist of nested layouts.</span></span> <span data-ttu-id="90a64-144">コンポーネントでは、別のレイアウトを参照するレイアウトを参照できます。</span><span class="sxs-lookup"><span data-stu-id="90a64-144">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="90a64-145">たとえば、複数レベルのメニュー構造を作成するために、レイアウトの入れ子を使用します。</span><span class="sxs-lookup"><span data-stu-id="90a64-145">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="90a64-146">次の例に、入れ子になったレイアウトの使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="90a64-146">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="90a64-147">*EpisodesComponent.razor* ファイルは、表示するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="90a64-147">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="90a64-148">コンポーネントは `MasterListLayout` を参照しています。</span><span class="sxs-lookup"><span data-stu-id="90a64-148">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="90a64-149">*MasterListLayout. razor* ファイルは `MasterListLayout` を提供します。</span><span class="sxs-lookup"><span data-stu-id="90a64-149">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="90a64-150">このレイアウトは、それがレンダリングされる別のレイアウト `MasterLayout` を参照しています。</span><span class="sxs-lookup"><span data-stu-id="90a64-150">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="90a64-151">`EpisodesComponent` は、`@Body` が表示される場所にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="90a64-151">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="90a64-152">最後に、*MasterLayout.razor* 内の `MasterLayout` に、ヘッダー、メイン メニュー、フッターなどの最上位レイアウト要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="90a64-152">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="90a64-153">`EpisodesComponent` を含む `MasterListLayout` は、`@Body` が表示される場所にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="90a64-153">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="90a64-154">統合コンポーネントと Razor Pages レイアウトを共有する</span><span class="sxs-lookup"><span data-stu-id="90a64-154">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="90a64-155">ルーティング可能なコンポーネントが Razor Pages アプリに統合されている場合、コンポーネントでアプリの共有レイアウトを使用できます。</span><span class="sxs-lookup"><span data-stu-id="90a64-155">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="90a64-156">詳細については、「<xref:blazor/integrate-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90a64-156">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90a64-157">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="90a64-157">Additional resources</span></span>

* <xref:mvc/views/layout>
