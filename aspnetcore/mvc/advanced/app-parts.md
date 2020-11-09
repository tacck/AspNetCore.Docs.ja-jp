---
title: RazorASP.NET Core のアプリケーションパーツを使用して、コントローラー、ビュー、ページなどを共有する
author: rick-anderson
description: RazorASP.NET Core のアプリケーションパーツを使用して、コントローラー、ビュー、ページなどを共有する
ms.author: riande
ms.date: 11/11/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061185"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a><span data-ttu-id="770bc-103">Razorアプリケーションパーツを使用して、コントローラー、ビュー、ページなどを共有する</span><span class="sxs-lookup"><span data-stu-id="770bc-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="770bc-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="770bc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="770bc-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="770bc-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="770bc-106">" *アプリケーション パーツ* " は、アプリのリソースを抽象化したものです。</span><span class="sxs-lookup"><span data-stu-id="770bc-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="770bc-107">アプリケーションパーツを使用すると、コントローラーの探索、コンポーネントの表示、ヘルパー、 Razor ページ、razor コンパイルソースなどの ASP.NET Core を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="770bc-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="770bc-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> はアプリケーション パーツです。</span><span class="sxs-lookup"><span data-stu-id="770bc-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="770bc-109">`AssemblyPart` はアセンブリ参照をカプセル化して、型とコンパイル参照を公開します。</span><span class="sxs-lookup"><span data-stu-id="770bc-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="770bc-110">"[機能プロバイダー](#fp)" は、アプリケーション パーツと連携し、ASP.NET Core アプリの機能を組み込みます。</span><span class="sxs-lookup"><span data-stu-id="770bc-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="770bc-111">アプリケーション パーツの主なユース ケースは、アセンブリから ASP.NET Core 機能を検出 (または読み込みを回避) するようにアプリを構成することです。</span><span class="sxs-lookup"><span data-stu-id="770bc-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="770bc-112">たとえば、複数のアプリで共通の機能を共有したいとします。</span><span class="sxs-lookup"><span data-stu-id="770bc-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="770bc-113">アプリケーションパーツを使用すると、複数のアプリを使用して、コントローラー、ビュー、 Razor ページ、razor コンパイルソース、タグヘルパーなどを含むアセンブリ (DLL) を共有できます。</span><span class="sxs-lookup"><span data-stu-id="770bc-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="770bc-114">複数のプロジェクトでコードを複製するよりもアセンブリの共有をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="770bc-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="770bc-115">ASP.NET Core アプリは <xref:System.Web.WebPages.ApplicationPart> から機能を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="770bc-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="770bc-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> クラスは、アセンブリでバックアップされるアプリケーション パーツを表します。</span><span class="sxs-lookup"><span data-stu-id="770bc-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="770bc-117">ASP.NET Core 機能の読み込み</span><span class="sxs-lookup"><span data-stu-id="770bc-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="770bc-118"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> および <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> クラスを使用し、ASP.NET Core 機能 (コントローラー、ビュー コンポーネントなど) を検出して読み込みます。</span><span class="sxs-lookup"><span data-stu-id="770bc-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="770bc-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> は、使用可能なアプリケーション パーツと機能プロバイダーを追跡します。</span><span class="sxs-lookup"><span data-stu-id="770bc-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="770bc-120">`ApplicationPartManager` は `Startup.ConfigureServices` で構成されます。</span><span class="sxs-lookup"><span data-stu-id="770bc-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="770bc-121">次のコードは、`AssemblyPart` を使用して `ApplicationPartManager` を構成する別の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="770bc-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="770bc-122">上記 2 つのコード サンプルでは、アセンブリから `SharedController` が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="770bc-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="770bc-123">`SharedController` は、アプリのプロジェクト内にありません。</span><span class="sxs-lookup"><span data-stu-id="770bc-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="770bc-124">[WebAppParts ソリューション](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts)のサンプル ダウンロードを参照してください。</span><span class="sxs-lookup"><span data-stu-id="770bc-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="770bc-125">ビューを含める</span><span class="sxs-lookup"><span data-stu-id="770bc-125">Include views</span></span>

<span data-ttu-id="770bc-126">[ Razor クラスライブラリ](xref:razor-pages/ui-class)を使用して、アセンブリにビューを含めます。</span><span class="sxs-lookup"><span data-stu-id="770bc-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="770bc-127">リソースの読み込みを防ぐ</span><span class="sxs-lookup"><span data-stu-id="770bc-127">Prevent loading resources</span></span>

<span data-ttu-id="770bc-128">アプリケーション パーツを使用して、特定のアセンブリまたは場所にあるソースの読み込みを " *回避* " することができます。</span><span class="sxs-lookup"><span data-stu-id="770bc-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="770bc-129"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> コレクションのメンバーを追加または削除して、リソースを非表示にしたり使用可能にしたりします。</span><span class="sxs-lookup"><span data-stu-id="770bc-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="770bc-130">`ApplicationParts` コレクションでのエントリの順序は重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="770bc-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="770bc-131">`ApplicationPartManager` を構成してから、コンテナーでサービスを構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="770bc-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="770bc-132">たとえば、`AddControllersAsServices` を呼び出す前に `ApplicationPartManager` を構成します。</span><span class="sxs-lookup"><span data-stu-id="770bc-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="770bc-133">リソースを削除するには、`ApplicationParts` コレクションに対して `Remove` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="770bc-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="770bc-134">`ApplicationPartManager` には次のパーツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="770bc-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="770bc-135">アプリのアセンブリおよび依存アセンブリ。</span><span class="sxs-lookup"><span data-stu-id="770bc-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="770bc-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="770bc-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="770bc-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="770bc-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="770bc-138">機能プロバイダー</span><span class="sxs-lookup"><span data-stu-id="770bc-138">Feature providers</span></span>

<span data-ttu-id="770bc-139">アプリケーション機能プロバイダーはアプリケーション パーツを調べ、これらのパーツの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="770bc-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="770bc-140">次の ASP.NET Core 機能には組み込みの機能プロバイダーがあります。</span><span class="sxs-lookup"><span data-stu-id="770bc-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="770bc-141">`internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="770bc-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="770bc-142">機能プロバイダーは <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> から継承されます。ここで `T` は機能の種類です。</span><span class="sxs-lookup"><span data-stu-id="770bc-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="770bc-143">機能プロバイダーは、前述の機能の種類のいずれについても実装できます。</span><span class="sxs-lookup"><span data-stu-id="770bc-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="770bc-144">`ApplicationPartManager.FeatureProviders` の機能プロバイダーの順序が実行時の動作に影響することがあります。</span><span class="sxs-lookup"><span data-stu-id="770bc-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="770bc-145">後から追加されたプロバイダーは、前に追加されたプロバイダーによって行われたアクションに対応できます。</span><span class="sxs-lookup"><span data-stu-id="770bc-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="770bc-146"> 使用可能な機能の表示</span><span class="sxs-lookup"><span data-stu-id="770bc-146">Display available features</span></span>

<span data-ttu-id="770bc-147">アプリで使用できる機能を列挙するには、[依存関係の挿入](../../fundamentals/dependency-injection.md)によって `ApplicationPartManager` を要求します。</span><span class="sxs-lookup"><span data-stu-id="770bc-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="770bc-148">[ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)では、前のコードを使用してアプリの機能を表示します。</span><span class="sxs-lookup"><span data-stu-id="770bc-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="770bc-149">アプリケーション パーツでの検出</span><span class="sxs-lookup"><span data-stu-id="770bc-149">Discovery in application parts</span></span>

<span data-ttu-id="770bc-150">アプリケーション パーツを使用した開発では、HTTP 404 エラーが発生することは珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="770bc-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="770bc-151">通常、これらのエラーは、アプリケーション パーツの検出に必要な要件が満たされていないことが原因で発生します。</span><span class="sxs-lookup"><span data-stu-id="770bc-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="770bc-152">アプリが HTTP 404 エラーを返す場合、次の要件が満たされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="770bc-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="770bc-153">`applicationName` 設定は、検出に使用されるルート アセンブリに設定されている必要がある。</span><span class="sxs-lookup"><span data-stu-id="770bc-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="770bc-154">検出に使用されるルート アセンブリは、通常、エントリ ポイント アセンブリです。</span><span class="sxs-lookup"><span data-stu-id="770bc-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="770bc-155">ルート アセンブリに、検出に使用されるパーツへの参照が設定されている必要がある。</span><span class="sxs-lookup"><span data-stu-id="770bc-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="770bc-156">この参照は、直接的な参照でも推移的な参照でも構いません。</span><span class="sxs-lookup"><span data-stu-id="770bc-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="770bc-157">ルート アセンブリは、Web SDK を参照する必要がある。</span><span class="sxs-lookup"><span data-stu-id="770bc-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="770bc-158">このフレームワークには、検出に使用されるルート アセンブリに属性のスタンプを付けるロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="770bc-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="770bc-159">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="770bc-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="770bc-160">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="770bc-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="770bc-161">" *アプリケーション パーツ* " は、アプリのリソースを抽象化したものです。</span><span class="sxs-lookup"><span data-stu-id="770bc-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="770bc-162">アプリケーションパーツを使用すると、コントローラーの探索、コンポーネントの表示、ヘルパー、 Razor ページ、razor コンパイルソースなどの ASP.NET Core を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="770bc-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="770bc-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) はアプリケーション パーツです。</span><span class="sxs-lookup"><span data-stu-id="770bc-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="770bc-164">`AssemblyPart` はアセンブリ参照をカプセル化して、型とコンパイル参照を公開します。</span><span class="sxs-lookup"><span data-stu-id="770bc-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="770bc-165">" *機能プロバイダー* " は、アプリケーション パーツと連携し、ASP.NET Core アプリの機能を組み込みます。</span><span class="sxs-lookup"><span data-stu-id="770bc-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="770bc-166">アプリケーション パーツの主なユース ケースは、アセンブリから ASP.NET Core 機能を検出 (または読み込みを回避) するようにアプリを構成することです。</span><span class="sxs-lookup"><span data-stu-id="770bc-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="770bc-167">たとえば、複数のアプリで共通の機能を共有したいとします。</span><span class="sxs-lookup"><span data-stu-id="770bc-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="770bc-168">アプリケーションパーツを使用すると、複数のアプリを使用して、コントローラー、ビュー、 Razor ページ、razor コンパイルソース、タグヘルパーなどを含むアセンブリ (DLL) を共有できます。</span><span class="sxs-lookup"><span data-stu-id="770bc-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="770bc-169">複数のプロジェクトでコードを複製するよりもアセンブリの共有をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="770bc-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="770bc-170">ASP.NET Core アプリは <xref:System.Web.WebPages.ApplicationPart> から機能を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="770bc-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="770bc-171"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> クラスは、アセンブリでバックアップされるアプリケーション パーツを表します。</span><span class="sxs-lookup"><span data-stu-id="770bc-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="770bc-172">ASP.NET Core 機能の読み込み</span><span class="sxs-lookup"><span data-stu-id="770bc-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="770bc-173">`ApplicationPart` および `AssemblyPart` クラスを使用し、ASP.NET Core 機能 (コントローラー、ビュー コンポーネントなど) を検出して読み込みます。</span><span class="sxs-lookup"><span data-stu-id="770bc-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="770bc-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> は、使用可能なアプリケーション パーツと機能プロバイダーを追跡します。</span><span class="sxs-lookup"><span data-stu-id="770bc-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="770bc-175">`ApplicationPartManager` は `Startup.ConfigureServices` で構成されます。</span><span class="sxs-lookup"><span data-stu-id="770bc-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="770bc-176">次のコードは、`AssemblyPart` を使用して `ApplicationPartManager` を構成する別の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="770bc-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="770bc-177">上記 2 つのコード サンプルでは、アセンブリから `SharedController` が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="770bc-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="770bc-178">`SharedController` は、アプリケーションのプロジェクト内にありません。</span><span class="sxs-lookup"><span data-stu-id="770bc-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="770bc-179">[WebAppParts ソリューション](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts)のサンプル ダウンロードを参照してください。</span><span class="sxs-lookup"><span data-stu-id="770bc-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="770bc-180">ビューを含める</span><span class="sxs-lookup"><span data-stu-id="770bc-180">Include views</span></span>

<span data-ttu-id="770bc-181">[ Razor クラスライブラリ](xref:razor-pages/ui-class)を使用して、アセンブリにビューを含めます。</span><span class="sxs-lookup"><span data-stu-id="770bc-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="770bc-182">リソースの読み込みを防ぐ</span><span class="sxs-lookup"><span data-stu-id="770bc-182">Prevent loading resources</span></span>

<span data-ttu-id="770bc-183">アプリケーション パーツを使用して、特定のアセンブリまたは場所にあるソースの読み込みを " *回避* " することができます。</span><span class="sxs-lookup"><span data-stu-id="770bc-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="770bc-184"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> コレクションのメンバーを追加または削除して、リソースを非表示にしたり使用可能にしたりします。</span><span class="sxs-lookup"><span data-stu-id="770bc-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="770bc-185">`ApplicationParts` コレクションでのエントリの順序は重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="770bc-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="770bc-186">`ApplicationPartManager` を構成してから、コンテナーでサービスを構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="770bc-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="770bc-187">たとえば、`AddControllersAsServices` を呼び出す前に `ApplicationPartManager` を構成します。</span><span class="sxs-lookup"><span data-stu-id="770bc-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="770bc-188">リソースを削除するには、`ApplicationParts` コレクションに対して `Remove` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="770bc-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="770bc-189">次のコードでは、<xref:Microsoft.AspNetCore.Mvc.ApplicationParts> を使用してアプリから `MyDependentLibrary` を削除します。[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="770bc-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="770bc-190">`ApplicationPartManager` には次のパーツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="770bc-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="770bc-191">アプリのアセンブリおよび依存アセンブリ。</span><span class="sxs-lookup"><span data-stu-id="770bc-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="770bc-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="770bc-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="770bc-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="770bc-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="770bc-194">アプリケーション機能プロバイダー</span><span class="sxs-lookup"><span data-stu-id="770bc-194">Application feature providers</span></span>

<span data-ttu-id="770bc-195">アプリケーション機能プロバイダーはアプリケーション パーツを調べ、これらのパーツの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="770bc-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="770bc-196">次の ASP.NET Core 機能には組み込みの機能プロバイダーがあります。</span><span class="sxs-lookup"><span data-stu-id="770bc-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="770bc-197">コントローラー</span><span class="sxs-lookup"><span data-stu-id="770bc-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="770bc-198">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="770bc-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="770bc-199">コンポーネントの表示</span><span class="sxs-lookup"><span data-stu-id="770bc-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="770bc-200">機能プロバイダーは <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> から継承されます。ここで `T` は機能の種類です。</span><span class="sxs-lookup"><span data-stu-id="770bc-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="770bc-201">機能プロバイダーは、前述の機能の種類のいずれについても実装できます。</span><span class="sxs-lookup"><span data-stu-id="770bc-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="770bc-202">`ApplicationPartManager.FeatureProviders` の機能プロバイダーの順序が実行時の動作に影響することがあります。</span><span class="sxs-lookup"><span data-stu-id="770bc-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="770bc-203">後から追加されたプロバイダーは、前に追加されたプロバイダーによって行われたアクションに対応できます。</span><span class="sxs-lookup"><span data-stu-id="770bc-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="770bc-204"> 使用可能な機能の表示</span><span class="sxs-lookup"><span data-stu-id="770bc-204">Display available features</span></span>

<span data-ttu-id="770bc-205">アプリで使用できる機能を列挙するには、[依存関係の挿入](../../fundamentals/dependency-injection.md)によって `ApplicationPartManager` を要求します。</span><span class="sxs-lookup"><span data-stu-id="770bc-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="770bc-206">[ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)では、前のコードを使用してアプリの機能を表示します。</span><span class="sxs-lookup"><span data-stu-id="770bc-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="770bc-207">アプリケーション パーツでの検出</span><span class="sxs-lookup"><span data-stu-id="770bc-207">Discovery in application parts</span></span>

<span data-ttu-id="770bc-208">アプリケーション パーツを使用した開発では、HTTP 404 エラーが発生することは珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="770bc-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="770bc-209">通常、これらのエラーは、アプリケーション パーツの検出に必要な要件が満たされていないことが原因で発生します。</span><span class="sxs-lookup"><span data-stu-id="770bc-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="770bc-210">アプリが HTTP 404 エラーを返す場合、次の要件が満たされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="770bc-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="770bc-211">`applicationName` 設定は、検出に使用されるルート アセンブリに設定されている必要がある。</span><span class="sxs-lookup"><span data-stu-id="770bc-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="770bc-212">検出に使用されるルート アセンブリは、通常、エントリ ポイント アセンブリです。</span><span class="sxs-lookup"><span data-stu-id="770bc-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="770bc-213">ルート アセンブリに、検出に使用されるパーツへの参照が設定されている必要がある。</span><span class="sxs-lookup"><span data-stu-id="770bc-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="770bc-214">この参照は、直接的な参照でも推移的な参照でも構いません。</span><span class="sxs-lookup"><span data-stu-id="770bc-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="770bc-215">ルート アセンブリは、Web SDK を参照する必要がある。</span><span class="sxs-lookup"><span data-stu-id="770bc-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="770bc-216">この ASP.NET Core フレームワークには、検出に使用されるルート アセンブリに属性のスタンプを付けるカスタム ビルド ロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="770bc-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
