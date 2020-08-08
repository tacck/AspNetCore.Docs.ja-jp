---
title: RazorASP.NET Core でのファイルのコンパイル
author: rick-anderson
description: ASP.NET Core アプリでファイルをコンパイルする方法について説明し Razor ます。
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: fc7924f8f8b321ae017b7acd729fe11c4e0e3c7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021082"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="a9b3b-103">RazorASP.NET Core でのファイルのコンパイル</span><span class="sxs-lookup"><span data-stu-id="a9b3b-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="a9b3b-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9b3b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="a9b3b-105">Razor拡張子が*cshtml*のファイルは、 [ Razor SDK](xref:razor-pages/sdk)を使用してビルド時と発行時の両方でコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="a9b3b-106">プロジェクトを構成することによって、必要に応じてランタイムコンパイルを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="a9b3b-107">Razorasp.net</span><span class="sxs-lookup"><span data-stu-id="a9b3b-107">Razor compilation</span></span>

<span data-ttu-id="a9b3b-108">ファイルのビルド時および発行時のコンパイル Razor は、SDK によって既定で有効になってい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a9b3b-109">有効にすると、ランタイムコンパイルはビルド時のコンパイルを補完し、 Razor 編集されている場合はファイルを更新できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="a9b3b-110">プロジェクトの作成時にランタイムコンパイルを有効にする</span><span class="sxs-lookup"><span data-stu-id="a9b3b-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="a9b3b-111">RazorPages および MVC プロジェクトテンプレートには、プロジェクトの作成時にランタイムコンパイルを有効にするオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="a9b3b-112">このオプションは ASP.NET Core 3.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9b3b-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9b3b-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9b3b-114">[**新しい ASP.NET Core web アプリケーションの作成**] ダイアログで次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="a9b3b-115">[ **Web アプリケーション**] プロジェクトテンプレートまたは [ **Web アプリケーション (モデルビューコントローラー)** ] プロジェクトテンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="a9b3b-116">[ \*\* Razor ランタイムコンパイルを有効にする\*\*] チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a9b3b-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9b3b-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a9b3b-118">`-rrc`または `--razor-runtime-compilation` テンプレートオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="a9b3b-119">たとえば、次のコマンドでは、 Razor ランタイムコンパイルが有効になっている新しい Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="a9b3b-120">既存のプロジェクトでランタイムコンパイルを有効にする</span><span class="sxs-lookup"><span data-stu-id="a9b3b-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="a9b3b-121">既存のプロジェクトのすべての環境に対してランタイムコンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="a9b3b-122">AspNetCore をインストールします。 [ RazorRuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="a9b3b-123">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、<xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="a9b3b-124">例:</span><span class="sxs-lookup"><span data-stu-id="a9b3b-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="a9b3b-125">既存のプロジェクトでのランタイムコンパイルを条件付きで有効にする</span><span class="sxs-lookup"><span data-stu-id="a9b3b-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="a9b3b-126">実行時コンパイルをローカル開発でのみ有効にできます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="a9b3b-127">このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="a9b3b-128">コンパイル済みのビューを使用する。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-128">Uses compiled views.</span></span>
* <span data-ttu-id="a9b3b-129">運用環境のファイル監視を有効にしない。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="a9b3b-130">開発環境でのみランタイムコンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="a9b3b-131">AspNetCore をインストールします。 [ RazorRuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="a9b3b-132">launchSettings.jsの [起動プロファイル `environmentVariables` ] セクションを次*のように*変更します。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="a9b3b-133">`ASPNETCORE_ENVIRONMENT`がに設定されていることを確認し `"Development"` ます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="a9b3b-134">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` を `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="a9b3b-135">次の例では、および起動プロファイルの開発環境でランタイムコンパイルが有効になってい `IIS Express` `RazorPagesApp` ます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="a9b3b-136">プロジェクトのクラスでコードを変更する必要はありません `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="a9b3b-137">実行時に、ASP.NET Core によって、で[アセンブリレベルの HostingStartup 属性](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute)が検索さ `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` れます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="a9b3b-138">属性は、 `HostingStartup` 実行するアプリスタートアップコードを指定します。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="a9b3b-139">このスタートアップコードは、ランタイムコンパイルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="a9b3b-140">クラスライブラリのランタイムコンパイルを有効にする Razor</span><span class="sxs-lookup"><span data-stu-id="a9b3b-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="a9b3b-141">Razorページプロジェクトが*MyClassLib*という名前の[ Razor クラスライブラリ (rcl)](xref:razor-pages/ui-class)を参照するシナリオについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="a9b3b-142">RCL には、すべてのチームの MVC およびページプロジェクトで使用される *_Layout の cshtml*ファイルが含まれています Razor 。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="a9b3b-143">その RCL の *_Layout*ファイルのランタイムコンパイルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="a9b3b-144">Pages プロジェクトで次の変更を行い Razor ます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="a9b3b-145">「[条件付きで既存のプロジェクトのランタイムコンパイルを有効にする」](#conditionally-enable-runtime-compilation-in-an-existing-project)の手順に従って、ランタイムコンパイルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="a9b3b-146">でランタイムコンパイルオプションを構成し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="a9b3b-147">前のコードでは、 *MyClassLib* rcl への絶対パスが構築されています。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="a9b3b-148">[Physicalfileprovider API](xref:fundamentals/file-providers#physicalfileprovider)は、その絶対パスでディレクトリとファイルを検索するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="a9b3b-149">最後に、 `PhysicalFileProvider` インスタンスがファイルプロバイダーコレクションに追加されます。これにより、RCL の*cshtml*ファイルにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9b3b-150">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a9b3b-150">Additional resources</span></span>

* <span data-ttu-id="a9b3b-151">[ Razor CompileOnBuild プロパティと Razor CompileOnPublish](xref:razor-pages/sdk#properties)プロパティ。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="a9b3b-152">Razor拡張子が*cshtml*のファイルは、 [ Razor SDK](xref:razor-pages/sdk)を使用してビルド時と発行時の両方でコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="a9b3b-153">実行時コンパイルは、アプリケーションを構成することで必要に応じて有効にできることがあります。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="a9b3b-154">Razorasp.net</span><span class="sxs-lookup"><span data-stu-id="a9b3b-154">Razor compilation</span></span>

<span data-ttu-id="a9b3b-155">ファイルのビルド時および発行時のコンパイル Razor は、SDK によって既定で有効になってい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a9b3b-156">有効にすると、ランタイムコンパイルはビルド時のコンパイルを補完し、 Razor 編集されている場合はファイルを更新できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="a9b3b-157">実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="a9b3b-157">Runtime compilation</span></span>

<span data-ttu-id="a9b3b-158">すべての環境と構成モードで実行時コンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="a9b3b-159">AspNetCore をインストールします。 [ RazorRuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="a9b3b-160">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、<xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="a9b3b-161">例:</span><span class="sxs-lookup"><span data-stu-id="a9b3b-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="a9b3b-162">実行時コンパイルを条件付きで有効にする</span><span class="sxs-lookup"><span data-stu-id="a9b3b-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="a9b3b-163">実行時コンパイルをローカル開発でのみ有効にできます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="a9b3b-164">このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="a9b3b-165">コンパイル済みのビューを使用する。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-165">Uses compiled views.</span></span>
* <span data-ttu-id="a9b3b-166">サイズが小さい。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-166">Is smaller in size.</span></span>
* <span data-ttu-id="a9b3b-167">運用環境のファイル監視を有効にしない。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="a9b3b-168">環境や構成モードに基づく実行時コンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="a9b3b-169">条件付きで AspNetCore を参照します。 [ Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)アクティブな値に基づいてパッケージを RuntimeCompilation `Configuration` :</span><span class="sxs-lookup"><span data-stu-id="a9b3b-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="a9b3b-170">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="a9b3b-171">`ASPNETCORE_ENVIRONMENT` 変数が `Development` に設定されている場合にのみ Debug モードで実行されるように、条件付きで `AddRazorRuntimeCompilation` を実行します。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="a9b3b-172">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a9b3b-172">Additional resources</span></span>

* <span data-ttu-id="a9b3b-173">[ Razor CompileOnBuild プロパティと Razor CompileOnPublish](xref:razor-pages/sdk#properties)プロパティ。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="a9b3b-174">プロジェクト間でランタイムコンパイルを行う方法を示すサンプルについては、 [GitHub のランタイムコンパイルサンプル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9b3b-175">ファイルは、 Razor 関連付けられた Razor ページまたは MVC ビューが呼び出されたときに、実行時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="a9b3b-176">Razorファイルは、 [ Razor SDK](xref:razor-pages/sdk)を使用してビルド時と発行時の両方でコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="a9b3b-177">Razorasp.net</span><span class="sxs-lookup"><span data-stu-id="a9b3b-177">Razor compilation</span></span>

<span data-ttu-id="a9b3b-178">ファイルのビルドおよび発行時のコンパイル Razor は、SDK によって既定で有効になってい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a9b3b-179">Razor更新後のファイルの編集は、ビルド時にサポートされます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="a9b3b-180">既定では、コンパイルされた*Views.dll*と、ファイルのコンパイルに必要なファイルまたは参照アセンブリは、 *.cshtml* Razor アプリと共に配置されます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a9b3b-181">プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a9b3b-182">[ Razor Sdk](xref:razor-pages/sdk)に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="a9b3b-183">SDK は、 Razor プリコンパイル固有のプロパティがプロジェクトファイルに設定されていない場合にのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="a9b3b-184">たとえば、 *.csproj*ファイルの `MvcRazorCompileOnPublish` プロパティをに設定すると、 `true` SDK が無効になり Razor ます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="a9b3b-185">実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="a9b3b-185">Runtime compilation</span></span>

<span data-ttu-id="a9b3b-186">ビルド時のコンパイルは、ファイルのランタイムコンパイルによって補完され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="a9b3b-187">ASP.NET Core Razor のファイルの内容が変更されると、MVC によってファイルが再コンパイルさ*れます*。</span><span class="sxs-lookup"><span data-stu-id="a9b3b-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9b3b-188">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a9b3b-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
