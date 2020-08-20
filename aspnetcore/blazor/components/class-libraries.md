---
title: ASP.NET Core Razor コンポーネント クラス ライブラリ
author: guardrex
description: 外部コンポーネント ライブラリから、コンポーネントを Blazor アプリに含める方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: d933a677a063d50fbe708264106e3ce19400a270
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628574"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="93b4e-103">ASP.NET Core Razor コンポーネント クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="93b4e-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="93b4e-104">作成者: [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="93b4e-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="93b4e-105">コンポーネントは、プロジェクト間で [Razor クラス ライブラリ (RCL)](xref:razor-pages/ui-class) で共有できます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="93b4e-106">*Razor コンポーネント クラス ライブラリ*は、次から含めることができます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="93b4e-107">ソリューションの別のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="93b4e-107">Another project in the solution.</span></span>
* <span data-ttu-id="93b4e-108">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="93b4e-108">A NuGet package.</span></span>
* <span data-ttu-id="93b4e-109">参照されている .NET ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="93b4e-109">A referenced .NET library.</span></span>

<span data-ttu-id="93b4e-110">コンポーネントが通常の .NET 型であるのと同様に、RCL によって提供されるコンポーネントは通常の .NET アセンブリです。</span><span class="sxs-lookup"><span data-stu-id="93b4e-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="93b4e-111">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="93b4e-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93b4e-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93b4e-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="93b4e-113">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-113">Create a new project.</span></span>
1. <span data-ttu-id="93b4e-114">**[Razor クラス ライブラリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="93b4e-115">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-115">Select **Next**.</span></span>
1. <span data-ttu-id="93b4e-116">**[新しい Razor クラス ライブラリを作成します]** ダイアログで **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="93b4e-117">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="93b4e-118">このトピックの例では、プロジェクト名 `ComponentLibrary` を使用します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="93b4e-119">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-119">Select **Create**.</span></span>
1. <span data-ttu-id="93b4e-120">RCL をソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="93b4e-121">ソリューションを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="93b4e-121">Right-click the solution.</span></span> <span data-ttu-id="93b4e-122">**[追加]**  >  **[既存のプロジェクト]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="93b4e-123">RCL のプロジェクト ファイルに移動します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="93b4e-124">RCL のプロジェクト ファイル (`.csproj`) を選択します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="93b4e-125">アプリから RCL の参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="93b4e-126">アプリ プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="93b4e-126">Right-click the app project.</span></span> <span data-ttu-id="93b4e-127">**[追加]**  >  **[参照]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="93b4e-128">RCL プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-128">Select the RCL project.</span></span> <span data-ttu-id="93b4e-129">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="93b4e-130">テンプレートから RCL を生成するときに **[ページとビューのサポート]** チェック ボックスがオンになっている場合は、生成したプロジェクトのルートに、次の内容で `_Imports.razor` ファイルも追加して、Razor コンポーネントを作成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="93b4e-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="93b4e-131">生成されたプロジェクトのルートにファイルを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="93b4e-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="93b4e-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="93b4e-133">コマンド シェルで [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用して、 **Razor クラス ライブラリ** テンプレート (`razorclasslib`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="93b4e-134">次の例では、`ComponentLibrary` という名前の RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="93b4e-135">コマンドの実行時に、`ComponentLibrary` を保持するフォルダーが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="93b4e-136">テンプレートから RCL を生成するときに、`-s|--support-pages-and-views` スイッチが使用されている場合、生成したプロジェクトのルートに、次の内容で `_Imports.razor` ファイルも追加して、Razor コンポーネントを作成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="93b4e-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="93b4e-137">生成されたプロジェクトのルートにファイルを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="93b4e-138">既存のプロジェクトにライブラリを追加するには、コマンド シェルで [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="93b4e-139">次の例では、RCL がアプリに追加されています。</span><span class="sxs-lookup"><span data-stu-id="93b4e-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="93b4e-140">ライブラリへのパスを使用して、アプリのプロジェクト フォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="93b4e-141">ライブラリ コンポーネントの使用</span><span class="sxs-lookup"><span data-stu-id="93b4e-141">Consume a library component</span></span>

<span data-ttu-id="93b4e-142">別のプロジェクトのライブラリに定義されているコンポーネントを使用するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="93b4e-143">名前空間と完全な型名を使用します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="93b4e-144">Razor の [`@using`](xref:mvc/views/razor#using) ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="93b4e-145">個々のコンポーネントを名前で追加することができます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-145">Individual components can be added by name.</span></span>

<span data-ttu-id="93b4e-146">次の例で、`ComponentLibrary` は `Component1` コンポーネント (`Component1.razor`) を含むコンポーネント ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="93b4e-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="93b4e-147">`Component1` コンポーネントは、ライブラリの作成時に RCL プロジェクト テンプレートによって自動的に追加されるサンプルのコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="93b4e-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="93b4e-148">`Component1` コンポーネントをその名前空間を使用して参照します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="93b4e-149">または、[`@using`](xref:mvc/views/razor#using) ディレクティブを使用してライブラリをスコープ内に取り込み、名前空間なしでコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="93b4e-150">必要に応じて、最上位の `_Import.razor` ファイルに `@using ComponentLibrary` ディレクティブを含めて、プロジェクト全体でライブラリのコンポーネントを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="93b4e-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="93b4e-151">ディレクティブを任意のレベルの `_Import.razor` ファイルに追加して、名前空間をフォルダー内の 1 つのコンポーネントまたは複数のコンポーネントに適用します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="93b4e-152">`Component1` の `my-component`CSS クラスをコンポーネントに提供するには、フレームワークの `Component1.razor` 内の [`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用して、ライブラリのスタイルシートにリンクします。</span><span class="sxs-lookup"><span data-stu-id="93b4e-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="93b4e-153">アプリ全体にスタイルシートを提供するには、アプリの `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) 内でライブラリのスタイルシートにリンクすることもできます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="93b4e-154">`Link` コンポーネントが子コンポーネントで使用されている場合、`Link` コンポーネントを持つ子がレンダリングされていれば、親コンポーネントのその他の子コンポーネントでもリンクされたアセットを使用できます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="93b4e-155">子コンポーネントで `Link`コンポーネントを使用することと、`wwwroot/index.html` または `Pages/_Host.cshtml` に `<link>` HTML タグを配置することの違いは、フレームワーク コンポーネントのレンダリングされた HTML タグが次のようになることです。</span><span class="sxs-lookup"><span data-stu-id="93b4e-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="93b4e-156">アプリケーションの状態によって変更できます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-156">Can be modified by application state.</span></span> <span data-ttu-id="93b4e-157">ハードコーディングされた `<link>` HTML タグは、アプリケーションの状態によって変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="93b4e-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="93b4e-158">親コンポーネントがレンダリングされなくなると、HTML `<head>` から削除されます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="93b4e-159">`Component1` の `my-component`CSS クラスを提供するには、アプリの `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) 内でライブラリのスタイルシートにリンクします。</span><span class="sxs-lookup"><span data-stu-id="93b4e-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="93b4e-160">静的アセットを含む Razor コンポーネント クラス ライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="93b4e-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="93b4e-161">RCL には、静的アセットを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-161">An RCL can include static assets.</span></span> <span data-ttu-id="93b4e-162">静的アセットは、ライブラリを使用するすべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="93b4e-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="93b4e-163">詳細については、「<xref:razor-pages/ui-class#create-an-rcl-with-static-assets>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93b4e-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="93b4e-164">複数のホスト型 Blazor アプリにコンポーネントと静的アセットを提供する</span><span class="sxs-lookup"><span data-stu-id="93b4e-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="93b4e-165">詳細については、「<xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93b4e-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="93b4e-166">ビルド、パック、NuGet への配布</span><span class="sxs-lookup"><span data-stu-id="93b4e-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="93b4e-167">コンポーネント ライブラリは標準 .NET ライブラリであるため、それらをパッケージ化して NuGet に配布することは、ライブラリをパッケージ化して NuGet に配布する場合と変わりはありません。</span><span class="sxs-lookup"><span data-stu-id="93b4e-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="93b4e-168">パッケージ化は、コマンド シェルで [`dotnet pack`](/dotnet/core/tools/dotnet-pack) コマンドを使用して実行します。</span><span class="sxs-lookup"><span data-stu-id="93b4e-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="93b4e-169">コマンド シェルで [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) コマンドを使用して、パッケージを NuGet にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="93b4e-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93b4e-170">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="93b4e-170">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="93b4e-171">XML リンカー構成ファイルをライブラリに追加する</span><span class="sxs-lookup"><span data-stu-id="93b4e-171">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
