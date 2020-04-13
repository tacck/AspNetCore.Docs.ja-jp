---
title: ASP.NET Core での Razor ファイルのコンパイル
author: rick-anderson
description: ASP.NET Core アプリで Razor ファイルのコンパイルがどのように行われるかについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223960"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="be57e-103">ASP.NET Core での Razor ファイルのコンパイル</span><span class="sxs-lookup"><span data-stu-id="be57e-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="be57e-104">著者 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="be57e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be57e-105">*.cshtml* 拡張子の Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="be57e-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="be57e-106">実行時コンパイルは、アプリケーションを構成することで必要に応じて有効にできることがあります。</span><span class="sxs-lookup"><span data-stu-id="be57e-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="be57e-107">Razor コンパイル</span><span class="sxs-lookup"><span data-stu-id="be57e-107">Razor compilation</span></span>

<span data-ttu-id="be57e-108">Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。</span><span class="sxs-lookup"><span data-stu-id="be57e-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="be57e-109">有効になっていると、実行時コンパイルはビルド時のコンパイルを補完し、編集された Razor ファイルを更新できます。</span><span class="sxs-lookup"><span data-stu-id="be57e-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="be57e-110">実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="be57e-110">Runtime compilation</span></span>

<span data-ttu-id="be57e-111">すべての環境と構成モードで実行時コンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="be57e-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="be57e-112">パッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)インストールします。</span><span class="sxs-lookup"><span data-stu-id="be57e-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="be57e-113">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、<xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="be57e-113">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="be57e-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="be57e-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="be57e-115">実行時コンパイルを条件付きで有効にする</span><span class="sxs-lookup"><span data-stu-id="be57e-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="be57e-116">実行時コンパイルをローカル開発でのみ有効にできます。</span><span class="sxs-lookup"><span data-stu-id="be57e-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="be57e-117">このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。</span><span class="sxs-lookup"><span data-stu-id="be57e-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="be57e-118">コンパイル済みのビューを使用する。</span><span class="sxs-lookup"><span data-stu-id="be57e-118">Uses compiled views.</span></span>
* <span data-ttu-id="be57e-119">サイズが小さい。</span><span class="sxs-lookup"><span data-stu-id="be57e-119">Is smaller in size.</span></span>
* <span data-ttu-id="be57e-120">運用環境のファイル監視を有効にしない。</span><span class="sxs-lookup"><span data-stu-id="be57e-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="be57e-121">環境や構成モードに基づく実行時コンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="be57e-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="be57e-122">アクティブな `Configuration` 値に基づいて、[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) パッケージを条件付きで参照します。</span><span class="sxs-lookup"><span data-stu-id="be57e-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="be57e-123">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="be57e-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="be57e-124">`ASPNETCORE_ENVIRONMENT` 変数が `Development` に設定されている場合にのみ Debug モードで実行されるように、条件付きで `AddRazorRuntimeCompilation` を実行します。</span><span class="sxs-lookup"><span data-stu-id="be57e-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="be57e-125">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="be57e-125">Additional resources</span></span>

* <span data-ttu-id="be57e-126">[プロパティを構築し、カミソリコンパイルオンを発行します](xref:razor-pages/sdk#properties)。</span><span class="sxs-lookup"><span data-stu-id="be57e-126">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="be57e-127">プロジェクト間[でのランタイム コンパイル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation)の動作を示すサンプルについては、GitHub のランタイム コンパイル のサンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="be57e-127">See the [runtimecompilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be57e-128">関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="be57e-128">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="be57e-129">Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="be57e-129">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="be57e-130">Razor コンパイル</span><span class="sxs-lookup"><span data-stu-id="be57e-130">Razor compilation</span></span>

<span data-ttu-id="be57e-131">Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。</span><span class="sxs-lookup"><span data-stu-id="be57e-131">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="be57e-132">更新後の Razor ファイルの編集は、ビルド時にサポートされています。</span><span class="sxs-lookup"><span data-stu-id="be57e-132">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="be57e-133">既定では、コンパイルされた *Views.dll* のみがアプリと共に展開されます。Razor ファイルのコンパイルに必要な *.cshtml* ファイルまたは参照アセンブリはアプリと共に展開されません。</span><span class="sxs-lookup"><span data-stu-id="be57e-133">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="be57e-134">プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。</span><span class="sxs-lookup"><span data-stu-id="be57e-134">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="be57e-135">[Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="be57e-135">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="be57e-136">Razor SDK は、プロジェクト ファイルにプリコンパイル固有のプロパティが設定されていない場合のみ有効です。</span><span class="sxs-lookup"><span data-stu-id="be57e-136">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="be57e-137">たとえば、*.csproj* ファイルの `MvcRazorCompileOnPublish` プロパティを `true` に設定して、Razor SDK を無効にします。</span><span class="sxs-lookup"><span data-stu-id="be57e-137">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="be57e-138">実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="be57e-138">Runtime compilation</span></span>

<span data-ttu-id="be57e-139">ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。</span><span class="sxs-lookup"><span data-stu-id="be57e-139">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="be57e-140">ASP.NET Core MVC は、*.cshtml* ファイルの内容が変更されたとき、Razor ファイルを再コンパイルします。</span><span class="sxs-lookup"><span data-stu-id="be57e-140">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be57e-141">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="be57e-141">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
