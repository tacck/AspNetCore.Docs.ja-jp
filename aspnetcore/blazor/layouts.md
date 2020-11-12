---
title: 'ASP.NET Core :::no-loc(Blazor)::: レイアウト'
author: guardrex
description: ':::no-loc(Blazor)::: アプリの再利用可能なレイアウト コンポーネントを作成する方法について説明します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/layouts
ms.openlocfilehash: 9462b73ad67394e79de08e7d2b13bf6a3145a04e
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507968"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="8099d-103">ASP.NET Core :::no-loc(Blazor)::: レイアウト</span><span class="sxs-lookup"><span data-stu-id="8099d-103">ASP.NET Core :::no-loc(Blazor)::: layouts</span></span>

<span data-ttu-id="8099d-104">作成者: [Rainer Stropek](https://www.timecockpit.com)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8099d-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8099d-105">メニュー、著作権メッセージ、会社のロゴなどの一部のアプリ要素は、通常、アプリの全体のレイアウトの一部であり、アプリのすべてのコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8099d-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="8099d-106">これらの要素のコードをアプリのすべてのコンポーネントにコピーするのは、効率的な方法ではありません。</span><span class="sxs-lookup"><span data-stu-id="8099d-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="8099d-107">要素の 1 つに更新が必要になるたびに、すべてのコンポーネントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8099d-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="8099d-108">このような複製を維持することは困難であり、時間の経過と共にコンテンツの一貫性が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8099d-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="8099d-109">*レイアウト* によって、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="8099d-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="8099d-110">技術的に、レイアウトはもう 1 つのコンポーネントにすぎません。</span><span class="sxs-lookup"><span data-stu-id="8099d-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="8099d-111">レイアウトは :::no-loc(Razor)::: テンプレートまたは C# コードで定義され、[データ バインディング](xref:blazor/components/data-binding)、[依存関係の挿入](xref:blazor/fundamentals/dependency-injection)、その他のコンポーネント シナリオを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8099d-111">A layout is defined in a :::no-loc(Razor)::: template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="8099d-112">*コンポーネント* を *レイアウト* に変えるには、コンポーネントが:</span><span class="sxs-lookup"><span data-stu-id="8099d-112">To turn a *component* into a *layout* , the component:</span></span>

* <span data-ttu-id="8099d-113">レイアウト内のレンダリングされるコンテンツの <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> プロパティを定義する <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> から継承している。</span><span class="sxs-lookup"><span data-stu-id="8099d-113">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="8099d-114">:::no-loc(Razor)::: 構文 `@Body` を使用して、コンテンツがレンダリングされるレイアウト マークアップ内の場所を指定している。</span><span class="sxs-lookup"><span data-stu-id="8099d-114">Uses the :::no-loc(Razor)::: syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="8099d-115">次のコード サンプルに、レイアウト コンポーネント `MainLayout.razor` の :::no-loc(Razor)::: テンプレートを示します。</span><span class="sxs-lookup"><span data-stu-id="8099d-115">The following code sample shows the :::no-loc(Razor)::: template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="8099d-116">レイアウトは <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> を継承し、ナビゲーション バーとフッターの間に `@Body` を設定します。</span><span class="sxs-lookup"><span data-stu-id="8099d-116">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a><span data-ttu-id="8099d-117">`MainLayout` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8099d-117">`MainLayout` component</span></span>

<span data-ttu-id="8099d-118">:::no-loc(Blazor)::: プロジェクト テンプレートのいずれかに基づくアプリでは、`MainLayout` コンポーネント (`MainLayout.razor`) は、アプリの `Shared` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="8099d-118">In an app based on one of the :::no-loc(Blazor)::: project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a><span data-ttu-id="8099d-119">既定のレイアウト</span><span class="sxs-lookup"><span data-stu-id="8099d-119">Default layout</span></span>

<span data-ttu-id="8099d-120">アプリの `App.razor` ファイル内の <xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントに、既定のアプリ レイアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="8099d-120">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="8099d-121">既定の :::no-loc(Blazor)::: テンプレートによって提供される次の <xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは、既定のレイアウトを `MainLayout` コンポーネントに設定します。</span><span class="sxs-lookup"><span data-stu-id="8099d-121">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default :::no-loc(Blazor)::: templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="8099d-122"><xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> コンテンツの既定のレイアウトを指定するには、<xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> コンテンツの <xref:Microsoft.AspNetCore.Components.LayoutView> を指定します。</span><span class="sxs-lookup"><span data-stu-id="8099d-122">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="8099d-123"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントの詳細については、「<xref:blazor/fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8099d-123">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="8099d-124">ルーターでレイアウトを既定のレイアウトとして指定することは、コンポーネントごとまたはフォルダーごとにオーバーライドできるため、便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="8099d-124">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="8099d-125">ルーターを使用してアプリの既定のレイアウトを設定することは、最も一般的な技法であるため、お勧めします。</span><span class="sxs-lookup"><span data-stu-id="8099d-125">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="8099d-126">コンポーネントにレイアウトを指定する</span><span class="sxs-lookup"><span data-stu-id="8099d-126">Specify a layout in a component</span></span>

<span data-ttu-id="8099d-127">コンポーネントにレイアウトを適用するには、:::no-loc(Razor)::: ディレクティブ `@layout` を使用します。</span><span class="sxs-lookup"><span data-stu-id="8099d-127">Use the :::no-loc(Razor)::: directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="8099d-128">コンパイラでは `@layout` を <xref:Microsoft.AspNetCore.Components.LayoutAttribute> に変換します。これはコンポーネント クラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="8099d-128">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="8099d-129">次の `MasterList` コンポーネントのコンテンツは、`@Body` の位置にある `MasterLayout` に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="8099d-129">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="8099d-130">コンポーネントに直接レイアウトを指定すると、ルーターに設定された *既定のレイアウトまたは `_Imports.razor` からインポートされた `@layout` ディレクティブ* がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8099d-130">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="8099d-131">一元的なレイアウトの選択</span><span class="sxs-lookup"><span data-stu-id="8099d-131">Centralized layout selection</span></span>

<span data-ttu-id="8099d-132">アプリのすべてのフォルダーには、必要に応じて、`_Imports.razor` という名前のテンプレート ファイルを格納できます。</span><span class="sxs-lookup"><span data-stu-id="8099d-132">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="8099d-133">コンパイラにより、インポート ファイルに指定されたディレクティブが、同じフォルダー内とそのすべてのサブフォルダー内で再帰的にすべての :::no-loc(Razor)::: テンプレートに含まれます。</span><span class="sxs-lookup"><span data-stu-id="8099d-133">The compiler includes the directives specified in the imports file in all of the :::no-loc(Razor)::: templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="8099d-134">そのため、`@layout MyCoolLayout` を含む `_Imports.razor` ファイルにより、フォルダー内のすべてのコンポーネントで `MyCoolLayout` が確実に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8099d-134">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="8099d-135">フォルダーおよびサブフォルダー内のすべての `.razor` ファイルに `@layout MyCoolLayout` を繰り返し追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8099d-135">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="8099d-136">`@using` ディレクティブは、同じようにコンポーネントにも適用されます。</span><span class="sxs-lookup"><span data-stu-id="8099d-136">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="8099d-137">次の `_Imports.razor` ファイルでインポートされるもの:</span><span class="sxs-lookup"><span data-stu-id="8099d-137">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="8099d-138">`MyCoolLayout`。</span><span class="sxs-lookup"><span data-stu-id="8099d-138">`MyCoolLayout`.</span></span>
* <span data-ttu-id="8099d-139">同じフォルダーおよびサブフォルダー内のすべての :::no-loc(Razor)::: コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="8099d-139">All :::no-loc(Razor)::: components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="8099d-140">`:::no-loc(Blazor):::App1.Data` 名前空間。</span><span class="sxs-lookup"><span data-stu-id="8099d-140">The `:::no-loc(Blazor):::App1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="8099d-141">`_Imports.razor` ファイルは、:::no-loc(Razor)::: ビューおよびページに対する [_ViewImports.cshtml ファイル](xref:mvc/views/layout#importing-shared-directives)に似ていますが、:::no-loc(Razor)::: コンポーネント ファイルに限定して適用されます。</span><span class="sxs-lookup"><span data-stu-id="8099d-141">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for :::no-loc(Razor)::: views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to :::no-loc(Razor)::: component files.</span></span>

<span data-ttu-id="8099d-142">`_Imports.razor` にレイアウトを指定すると、ルーターの *既定のレイアウト* として指定されたレイアウトがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8099d-142">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="8099d-143">:::no-loc(Razor)::: `@layout` ディレクティブをルート `_Imports.razor` ファイルに追加 **しない** でください。アプリでレイアウトが無限ループになります。</span><span class="sxs-lookup"><span data-stu-id="8099d-143">Do **not** add a :::no-loc(Razor)::: `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="8099d-144">既定のアプリ レイアウトを制御するには、`Router` コンポーネントでレイアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="8099d-144">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="8099d-145">詳細については、「[既定のレイアウト](#default-layout)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8099d-145">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="8099d-146">入れ子になったレイアウト</span><span class="sxs-lookup"><span data-stu-id="8099d-146">Nested layouts</span></span>

<span data-ttu-id="8099d-147">アプリは、入れ子になったレイアウトで構成できます。</span><span class="sxs-lookup"><span data-stu-id="8099d-147">Apps can consist of nested layouts.</span></span> <span data-ttu-id="8099d-148">コンポーネントでは、別のレイアウトを参照するレイアウトを参照できます。</span><span class="sxs-lookup"><span data-stu-id="8099d-148">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="8099d-149">たとえば、複数レベルのメニュー構造を作成するために、レイアウトの入れ子を使用します。</span><span class="sxs-lookup"><span data-stu-id="8099d-149">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="8099d-150">次の例に、入れ子になったレイアウトの使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="8099d-150">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="8099d-151">`EpisodesComponent.razor` ファイルは、表示するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="8099d-151">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="8099d-152">コンポーネントは `MasterListLayout` を参照しています。</span><span class="sxs-lookup"><span data-stu-id="8099d-152">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="8099d-153">`MasterListLayout.razor` ファイルからは、`MasterListLayout` が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8099d-153">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="8099d-154">このレイアウトは、それがレンダリングされる別のレイアウト `MasterLayout` を参照しています。</span><span class="sxs-lookup"><span data-stu-id="8099d-154">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="8099d-155">`EpisodesComponent` は、`@Body` が表示される場所にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8099d-155">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="8099d-156">最後に、`MasterLayout.razor` 内の `MasterLayout` に、ヘッダー、メイン メニュー、フッターなどの最上位レイアウト要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8099d-156">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="8099d-157">`EpisodesComponent` を含む `MasterListLayout` は、`@Body` が表示される場所にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8099d-157">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="8099d-158">統合コンポーネントと :::no-loc(Razor)::: Pages レイアウトを共有する</span><span class="sxs-lookup"><span data-stu-id="8099d-158">Share a :::no-loc(Razor)::: Pages layout with integrated components</span></span>

<span data-ttu-id="8099d-159">ルーティング可能なコンポーネントが :::no-loc(Razor)::: Pages アプリに統合されている場合、コンポーネントでアプリの共有レイアウトを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8099d-159">When routable components are integrated into a :::no-loc(Razor)::: Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="8099d-160">詳細については、「<xref:blazor/components/prerendering-and-integration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8099d-160">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8099d-161">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8099d-161">Additional resources</span></span>

* <xref:mvc/views/layout>
