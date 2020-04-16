---
title: ASP.NET Core での Razor ファイルのコンパイル
author: rick-anderson
description: ASP.NET Core アプリで Razor ファイルのコンパイルがどのように行われるかについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440936"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="8904d-103">ASP.NET Core での Razor ファイルのコンパイル</span><span class="sxs-lookup"><span data-stu-id="8904d-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="8904d-104">著者 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8904d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="8904d-105">*.cshtml* 拡張子の Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="8904d-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="8904d-106">プロジェクトを構成することによって、ランタイム コンパイルを有効にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8904d-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="8904d-107">Razor コンパイル</span><span class="sxs-lookup"><span data-stu-id="8904d-107">Razor compilation</span></span>

<span data-ttu-id="8904d-108">Razor ファイルのビルド時および公開時のコンパイルは、Razor SDK によって既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="8904d-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="8904d-109">有効にすると、ランタイム コンパイルはビルド時のコンパイルを補完し、編集された場合に Razor ファイルを更新できるようにします。</span><span class="sxs-lookup"><span data-stu-id="8904d-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="8904d-110">プロジェクト作成時に実行時コンパイルを有効にする</span><span class="sxs-lookup"><span data-stu-id="8904d-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="8904d-111">Razor ページと MVC プロジェクト テンプレートには、プロジェクトの作成時にランタイム コンパイルを有効にするオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8904d-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="8904d-112">このオプションは、ASP.NET Core 3.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8904d-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8904d-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8904d-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8904d-114">[**新しいASP.NETコア Web アプリケーションの作成**] ダイアログで、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="8904d-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="8904d-115">**Web アプリケーション**または Web**アプリケーション (モデル ビュー コントローラー)** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="8904d-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="8904d-116">[Razor**ランタイム コンパイルを有効にする**] チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="8904d-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8904d-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8904d-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8904d-118">または`--razor-runtime-compilation``-rrc`テンプレート オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="8904d-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="8904d-119">たとえば、次のコマンドは、ランタイム コンパイルが有効になっている新しい Razor ページ プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="8904d-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="8904d-120">既存のプロジェクトでランタイム コンパイルを有効にする</span><span class="sxs-lookup"><span data-stu-id="8904d-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="8904d-121">既存のプロジェクトのすべての環境でランタイム コンパイルを有効にするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="8904d-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="8904d-122">パッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)インストールします。</span><span class="sxs-lookup"><span data-stu-id="8904d-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="8904d-123">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、<xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="8904d-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="8904d-124">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8904d-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="8904d-125">既存のプロジェクトで条件付きで実行時コンパイルを有効にする</span><span class="sxs-lookup"><span data-stu-id="8904d-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="8904d-126">実行時コンパイルをローカル開発でのみ有効にできます。</span><span class="sxs-lookup"><span data-stu-id="8904d-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="8904d-127">このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。</span><span class="sxs-lookup"><span data-stu-id="8904d-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="8904d-128">コンパイル済みのビューを使用する。</span><span class="sxs-lookup"><span data-stu-id="8904d-128">Uses compiled views.</span></span>
* <span data-ttu-id="8904d-129">運用環境のファイル監視を有効にしない。</span><span class="sxs-lookup"><span data-stu-id="8904d-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="8904d-130">開発環境でのみランタイム コンパイルを有効にするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="8904d-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="8904d-131">パッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)インストールします。</span><span class="sxs-lookup"><span data-stu-id="8904d-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="8904d-132">起動`environmentVariables`*設定.json*で起動プロファイルセクションを変更します。</span><span class="sxs-lookup"><span data-stu-id="8904d-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="8904d-133">確認`ASPNETCORE_ENVIRONMENT`は に`"Development"`設定されています。</span><span class="sxs-lookup"><span data-stu-id="8904d-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="8904d-134">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` を `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="8904d-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="8904d-135">次の例では、開発環境でランタイム コンパイルが有効化され、起動`IIS Express``RazorPagesApp`プロファイルが有効になります。</span><span class="sxs-lookup"><span data-stu-id="8904d-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="8904d-136">プロジェクトの`Startup`クラスでコードを変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8904d-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="8904d-137">実行時に、ASP.NET Core は、 で[アセンブリ レベルの HostingStartup 属性](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute)を`Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`検索します。</span><span class="sxs-lookup"><span data-stu-id="8904d-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="8904d-138">この`HostingStartup`属性は、実行するアプリのスタートアップ コードを指定します。</span><span class="sxs-lookup"><span data-stu-id="8904d-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="8904d-139">このスタートアップ コードは、実行時のコンパイルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="8904d-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="8904d-140">Razor クラス ライブラリのランタイム コンパイルを有効にする</span><span class="sxs-lookup"><span data-stu-id="8904d-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="8904d-141">Razor ページ プロジェクトが*MyClassLib*という名前の[Razor クラス ライブラリ (RCL) を](xref:razor-pages/ui-class)参照するシナリオを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8904d-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="8904d-142">RCL には、チームのすべての MVC および Razor ページ プロジェクトで使用される *_Layout.cshtml*ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8904d-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="8904d-143">その RCL 内の *_Layout.cshtml*ファイルの実行時コンパイルを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8904d-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="8904d-144">Razor ページ プロジェクトで次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="8904d-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="8904d-145">「既存のプロジェクトで条件付きの実行時コンパイルを有効にする」の手順を使用して[、ランタイム コンパイルを有効に](#conditionally-enable-runtime-compilation-in-an-existing-project)します。</span><span class="sxs-lookup"><span data-stu-id="8904d-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="8904d-146">でランタイム コンパイル オプション`Startup.ConfigureServices`を構成します。</span><span class="sxs-lookup"><span data-stu-id="8904d-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="8904d-147">上記のコードでは *、MyClassLib* RCL への絶対パスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8904d-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="8904d-148">[物理ファイル プロバイダ API](xref:fundamentals/file-providers#physicalfileprovider)は、その絶対パスにあるディレクトリとファイルを検索するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8904d-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="8904d-149">最後に`PhysicalFileProvider`、インスタンスは、RCL の *.cshtml*ファイルへのアクセスを許可するファイル プロバイダーコレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8904d-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8904d-150">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8904d-150">Additional resources</span></span>

* <span data-ttu-id="8904d-151">[プロパティを構築し、カミソリコンパイルオンを発行します](xref:razor-pages/sdk#properties)。</span><span class="sxs-lookup"><span data-stu-id="8904d-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="8904d-152">*.cshtml* 拡張子の Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="8904d-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="8904d-153">実行時コンパイルは、アプリケーションを構成することで必要に応じて有効にできることがあります。</span><span class="sxs-lookup"><span data-stu-id="8904d-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="8904d-154">Razor コンパイル</span><span class="sxs-lookup"><span data-stu-id="8904d-154">Razor compilation</span></span>

<span data-ttu-id="8904d-155">Razor ファイルのビルド時および公開時のコンパイルは、Razor SDK によって既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="8904d-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="8904d-156">有効にすると、ランタイム コンパイルはビルド時のコンパイルを補完し、編集された場合に Razor ファイルを更新できるようにします。</span><span class="sxs-lookup"><span data-stu-id="8904d-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="8904d-157">実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="8904d-157">Runtime compilation</span></span>

<span data-ttu-id="8904d-158">すべての環境と構成モードで実行時コンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="8904d-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="8904d-159">パッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)インストールします。</span><span class="sxs-lookup"><span data-stu-id="8904d-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="8904d-160">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、<xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="8904d-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="8904d-161">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8904d-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="8904d-162">実行時コンパイルを条件付きで有効にする</span><span class="sxs-lookup"><span data-stu-id="8904d-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="8904d-163">実行時コンパイルをローカル開発でのみ有効にできます。</span><span class="sxs-lookup"><span data-stu-id="8904d-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="8904d-164">このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。</span><span class="sxs-lookup"><span data-stu-id="8904d-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="8904d-165">コンパイル済みのビューを使用する。</span><span class="sxs-lookup"><span data-stu-id="8904d-165">Uses compiled views.</span></span>
* <span data-ttu-id="8904d-166">サイズが小さい。</span><span class="sxs-lookup"><span data-stu-id="8904d-166">Is smaller in size.</span></span>
* <span data-ttu-id="8904d-167">運用環境のファイル監視を有効にしない。</span><span class="sxs-lookup"><span data-stu-id="8904d-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="8904d-168">環境や構成モードに基づく実行時コンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="8904d-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="8904d-169">アクティブな `Configuration` 値に基づいて、[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) パッケージを条件付きで参照します。</span><span class="sxs-lookup"><span data-stu-id="8904d-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="8904d-170">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="8904d-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="8904d-171">`ASPNETCORE_ENVIRONMENT` 変数が `Development` に設定されている場合にのみ Debug モードで実行されるように、条件付きで `AddRazorRuntimeCompilation` を実行します。</span><span class="sxs-lookup"><span data-stu-id="8904d-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="8904d-172">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8904d-172">Additional resources</span></span>

* <span data-ttu-id="8904d-173">[プロパティを構築し、カミソリコンパイルオンを発行します](xref:razor-pages/sdk#properties)。</span><span class="sxs-lookup"><span data-stu-id="8904d-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="8904d-174">プロジェクト間[でのランタイム コンパイル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation)の動作を示すサンプルについては、GitHub のランタイム コンパイル サンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8904d-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8904d-175">関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="8904d-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="8904d-176">Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="8904d-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="8904d-177">Razor コンパイル</span><span class="sxs-lookup"><span data-stu-id="8904d-177">Razor compilation</span></span>

<span data-ttu-id="8904d-178">Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。</span><span class="sxs-lookup"><span data-stu-id="8904d-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="8904d-179">更新後の Razor ファイルの編集は、ビルド時にサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8904d-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="8904d-180">既定では、コンパイルされた *Views.dll* のみがアプリと共に展開されます。Razor ファイルのコンパイルに必要な *.cshtml* ファイルまたは参照アセンブリはアプリと共に展開されません。</span><span class="sxs-lookup"><span data-stu-id="8904d-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8904d-181">プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。</span><span class="sxs-lookup"><span data-stu-id="8904d-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8904d-182">[Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8904d-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="8904d-183">Razor SDK は、プロジェクト ファイルにプリコンパイル固有のプロパティが設定されていない場合のみ有効です。</span><span class="sxs-lookup"><span data-stu-id="8904d-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="8904d-184">たとえば、*.csproj* ファイルの `MvcRazorCompileOnPublish` プロパティを `true` に設定して、Razor SDK を無効にします。</span><span class="sxs-lookup"><span data-stu-id="8904d-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="8904d-185">実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="8904d-185">Runtime compilation</span></span>

<span data-ttu-id="8904d-186">ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。</span><span class="sxs-lookup"><span data-stu-id="8904d-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="8904d-187">ASP.NET Core MVC は、*.cshtml* ファイルの内容が変更されたとき、Razor ファイルを再コンパイルします。</span><span class="sxs-lookup"><span data-stu-id="8904d-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8904d-188">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8904d-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
