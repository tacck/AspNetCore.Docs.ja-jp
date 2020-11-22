---
title: ASP.NET Core Blazor の CSS の分離
author: daveabrock
description: CSS の分離を使用して、コンポーネントに CSS のスコープを設定する方法について説明します。これにより、CSS が簡素化され、他のコンポーネントやライブラリとの競合を回避できます。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 4fec0fa750b9209849030d0d6b7de8f4e163d62f
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570134"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="97f22-103">ASP.NET Core Blazor の CSS の分離</span><span class="sxs-lookup"><span data-stu-id="97f22-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="97f22-104">作成者: [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="97f22-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="97f22-105">CSS の分離により、グローバル スタイルへの依存を防ぐことで、アプリの CSS 占有領域を簡素化し、コンポーネントおよびライブラリ間のスタイルの競合を回避するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="97f22-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="97f22-106">CSS の分離を有効にする</span><span class="sxs-lookup"><span data-stu-id="97f22-106">Enable CSS isolation</span></span> 

<span data-ttu-id="97f22-107">コンポーネント固有のスタイルを定義するには、コンポーネントの `.razor` ファイルの名前と一致する `.razor.css` ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="97f22-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="97f22-108">この `.razor.css` ファイルは、"*スコープ付き CSS ファイル*" です。</span><span class="sxs-lookup"><span data-stu-id="97f22-108">This `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="97f22-109">`MyComponent.razor` ファイルを持つ `MyComponent` コンポーネントの場合、コンポーネントと共に `MyComponent.razor.css` という名前のファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="97f22-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="97f22-110">ファイル名 `.razor.css` の `MyComponent` 値では、大文字と小文字は区別 **されません**。</span><span class="sxs-lookup"><span data-stu-id="97f22-110">The `MyComponent` value in the `.razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="97f22-111">たとえば、既定の Blazor プロジェクト テンプレートの `Counter` コンポーネントに CSS の分離を追加するには、`Counter.razor` ファイルと共に `Counter.razor.css` という名前の新しいファイルを追加し、次の CSS を追加します。</span><span class="sxs-lookup"><span data-stu-id="97f22-111">For example to add CSS isolation to the `Counter` component in the default Blazor project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="97f22-112">`Counter.razor.css` で定義されるスタイルは、`Counter` コンポーネントのレンダリングされる出力にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="97f22-113">アプリ内の他の場所で定義されるどの `h1` CSS も、`Counter` スタイルと競合しません。</span><span class="sxs-lookup"><span data-stu-id="97f22-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="97f22-114">バンドルが発生したときにスタイルの分離を保証するために、`@import` Razor ブロックは、スコープ付き CSS ファイルではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="97f22-114">In order to guarantee style isolation when bundling occurs, `@import` Razor blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="97f22-115">CSS の分離のバンドル</span><span class="sxs-lookup"><span data-stu-id="97f22-115">CSS isolation bundling</span></span>

<span data-ttu-id="97f22-116">CSS の分離は、ビルド時に発生します。</span><span class="sxs-lookup"><span data-stu-id="97f22-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="97f22-117">このプロセス中、Blazor により、コンポーネントによってレンダリングされるマークアップと一致するように CSS セレクターが書き換えられます。</span><span class="sxs-lookup"><span data-stu-id="97f22-117">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="97f22-118">これらの書き換えられた CSS スタイルは、`{PROJECT NAME}.styles.css` でバンドルされ、静的アセットとして生成されます。ここで、プレースホルダー `{PROJECT NAME}` は、参照されるパッケージまたは製品名です。</span><span class="sxs-lookup"><span data-stu-id="97f22-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="97f22-119">既定では、これらの静的ファイルはアプリのルート パスから参照されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="97f22-120">アプリでは、生成された HTML の `<head>` タグ内の参照を調べることにより、バンドルされたファイルを参照します。</span><span class="sxs-lookup"><span data-stu-id="97f22-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="97f22-121">バンドルされたファイル内で、各コンポーネントはスコープ識別子に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="97f22-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="97f22-122">スタイルが設定されるコンポーネントごとに、HTML 属性が形式 `b-<10-character-string>` を使用して追加されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="97f22-123">識別子は一意であり、アプリごとに異なります。</span><span class="sxs-lookup"><span data-stu-id="97f22-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="97f22-124">レンダリングされる `Counter` コンポーネントでは、Blazor により、スコープ識別子が `h1` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-124">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="97f22-125">`MyProjectName.styles.css` ファイルは、スコープ識別子を使用してスタイル宣言をそのコンポーネントと共にグループ化します。</span><span class="sxs-lookup"><span data-stu-id="97f22-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="97f22-126">次の例では、前述の `<h1>` 要素のスタイルを示します。</span><span class="sxs-lookup"><span data-stu-id="97f22-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="97f22-127">ビルド時、規則 `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` でプロジェクト バンドルが作成されます。ここで、プレースホルダー `{STATIC WEB ASSETS BASE PATH}` は静的な Web アセットのベース パスです。</span><span class="sxs-lookup"><span data-stu-id="97f22-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="97f22-128">NuGet パッケージや [Razor クラス ライブラリ](xref:blazor/components/class-libraries)などの他のプロジェクトを利用する場合、バンドル ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="97f22-128">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="97f22-129">CSS インポートを使用してスタイルを参照する。</span><span class="sxs-lookup"><span data-stu-id="97f22-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="97f22-130">スタイルを使用するアプリの静的な Web アセットとして公開されない。</span><span class="sxs-lookup"><span data-stu-id="97f22-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="97f22-131">子コンポーネントのサポート</span><span class="sxs-lookup"><span data-stu-id="97f22-131">Child component support</span></span>

<span data-ttu-id="97f22-132">既定で、CSS の分離は形式 `{COMPONENT NAME}.razor.css` に関連付けられたコンポーネントにのみ適用されます。ここで、プレースホルダー `{COMPONENT NAME}` は、通常、コンポーネント名です。</span><span class="sxs-lookup"><span data-stu-id="97f22-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="97f22-133">子コンポーネントに変更を適用するには、親コンポーネントの `.razor.css` ファイル内の子孫要素に `::deep` 連結子を使用します。</span><span class="sxs-lookup"><span data-stu-id="97f22-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="97f22-134">`::deep` 連結子により、要素の生成されたスコープ識別子の "*子孫*" である要素が選択されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="97f22-135">次の例は、`Child` という名前の子コンポーネントを持つ `Parent` という名前の親コンポーネントを示します。</span><span class="sxs-lookup"><span data-stu-id="97f22-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="97f22-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="97f22-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="97f22-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="97f22-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="97f22-138">`::deep` 連結子を使用して `Parent.razor.css` 内の `h1` 宣言を更新し、`h1` スタイル宣言を親コンポーネントとその子に適用する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="97f22-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="97f22-139">これで、子コンポーネント用の個別のスコープ付き CSS ファイルを作成する必要なく、`h1` スタイルは、`Parent` と `Child` の各コンポーネントに適用されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="97f22-140">`::deep` 連結子は、子孫要素でのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="97f22-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="97f22-141">次の HTML 構造体では、想定どおりに `h1` スタイルがコンポーネントに適用されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="97f22-142">このシナリオでは、ASP.NET Core により、親コンポーネントのスコープ識別子が `div` 要素に適用されるため、ブラウザーでは、スタイルを親コンポーネントから継承することが認識されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="97f22-143">ただし、`div` 要素を除外すると、子孫関係が削除され、スタイルは子コンポーネントに適用 **されません**。</span><span class="sxs-lookup"><span data-stu-id="97f22-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="97f22-144">CSS プリプロセッサのサポート</span><span class="sxs-lookup"><span data-stu-id="97f22-144">CSS preprocessor support</span></span>

<span data-ttu-id="97f22-145">CSS プリプロセッサは、変数、入れ子、モジュール、mixin、継承などの機能を利用することで CSS 開発を改善するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="97f22-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="97f22-146">CSS の分離は、SASS や LESS などの CSS プリプロセッサをネイティブにサポートしていませんが、ビルド プロセス中に Blazor により CSS セレクターが書き換えられる前にプリプロセッサのコンパイルが行われる限り、CSS プリプロセッサの統合はシームレスです。</span><span class="sxs-lookup"><span data-stu-id="97f22-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="97f22-147">たとえば、Visual Studio を使用して、Visual Studio タスク ランナー エクスプローラーで既存のプリプロセッサ コンパイルを **ビルド前** タスクとして構成します。</span><span class="sxs-lookup"><span data-stu-id="97f22-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="97f22-148">[Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder) などの多くのサードパーティ製 NuGet パッケージは、CSS の分離が発生する前に、ビルド プロセスの開始時に SASS または SCSS ファイルをコンパイルでき、追加の構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="97f22-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="97f22-149">CSS の分離の構成</span><span class="sxs-lookup"><span data-stu-id="97f22-149">CSS isolation configuration</span></span>

<span data-ttu-id="97f22-150">CSS の分離は、すぐに使用できるように設計されていますが、既存のツールやワークフローへの依存関係がある場合など、一部の高度なシナリオ用の構成も用意されています。</span><span class="sxs-lookup"><span data-stu-id="97f22-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="97f22-151">スコープ識別子の形式をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="97f22-151">Customize scope identifier format</span></span>

<span data-ttu-id="97f22-152">既定では、スコープ識別子に形式 `b-<10-character-string>` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="97f22-153">スコープ識別子の形式をカスタマイズするには、プロジェクト ファイルを目的のパターンに更新します。</span><span class="sxs-lookup"><span data-stu-id="97f22-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="97f22-154">前の例では、`MyComponent.Razor.css` 用に生成された CSS により、そのスコープ識別子は `b-<10-character-string>` から `my-custom-scope-identifier` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-154">In the preceding example, the CSS generated for `MyComponent.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="97f22-155">静的な Web アセットのベース パスを変更する</span><span class="sxs-lookup"><span data-stu-id="97f22-155">Change base path for static web assets</span></span>

<span data-ttu-id="97f22-156">`scoped.styles.css` ファイルは、アプリのルートで生成されます。</span><span class="sxs-lookup"><span data-stu-id="97f22-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="97f22-157">プロジェクト ファイルでは、`StaticWebAssetBasePath` プロパティを使用して既定のパスを変更します。</span><span class="sxs-lookup"><span data-stu-id="97f22-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="97f22-158">次の例では、`scoped.styles.css` ファイルとアプリの残りのアセットを `_content` パスに配置します。</span><span class="sxs-lookup"><span data-stu-id="97f22-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="97f22-159">自動バンドルを無効にする</span><span class="sxs-lookup"><span data-stu-id="97f22-159">Disable automatic bundling</span></span>

<span data-ttu-id="97f22-160">Blazor でスコープ付きファイルを公開し、それを実行時に読み込む方法をオプトアウトするには、`DisableScopedCssBundling` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="97f22-160">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="97f22-161">このプロパティを使用する場合、`obj` ディレクトリからの CSS ファイルの分離と、それらの公開および実行時の読み込みを他のツールまたはプロセスが担当することを意味します。</span><span class="sxs-lookup"><span data-stu-id="97f22-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="no-locrazor-class-library-rcl-support"></a><span data-ttu-id="97f22-162">Razor クラス ライブラリ (RCL) のサポート</span><span class="sxs-lookup"><span data-stu-id="97f22-162">Razor class library (RCL) support</span></span>

<span data-ttu-id="97f22-163">[Razor クラス ライブラリ (RCL)](xref:razor-pages/ui-class) により分離スタイルが提供される場合、`<link>` タグの `href` 属性は `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` を指します。ここで、プレースホルダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="97f22-163">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="97f22-164">`{STATIC WEB ASSET BASE PATH}`: 静的な Web 資産のベース パス。</span><span class="sxs-lookup"><span data-stu-id="97f22-164">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="97f22-165">`{ASSEMBLY NAME}`: クラス ライブラリのアセンブリ名。</span><span class="sxs-lookup"><span data-stu-id="97f22-165">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="97f22-166">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="97f22-166">In the following example:</span></span>

* <span data-ttu-id="97f22-167">静的な Web 資産のベース パスは `_content/ClassLib` です。</span><span class="sxs-lookup"><span data-stu-id="97f22-167">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="97f22-168">クラス ライブラリのアセンブリ名は `ClassLib` です。</span><span class="sxs-lookup"><span data-stu-id="97f22-168">The class library's assembly name is `ClassLib`.</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="97f22-169">RCL およびコンポーネント ライブラリの詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97f22-169">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="97f22-170"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="97f22-170"><xref:blazor/components/class-libraries>.</span></span>
