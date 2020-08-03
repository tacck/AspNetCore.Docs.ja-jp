---
title: ASP.NET Core で複数の環境を使用する
author: rick-anderson
description: ASP.NET Core アプリで複数の環境にわたりアプリの動作を制御する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330640"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="28186-103">ASP.NET Core で複数の環境を使用する</span><span class="sxs-lookup"><span data-stu-id="28186-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="28186-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="28186-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="28186-105">ASP.NET Core は、環境変数を利用し、ランタイム環境に基づいてアプリの動作を構成します。</span><span class="sxs-lookup"><span data-stu-id="28186-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="28186-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="28186-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="28186-107">環境</span><span class="sxs-lookup"><span data-stu-id="28186-107">Environments</span></span>

<span data-ttu-id="28186-108">ランタイム環境を決定するために、ASP.NET Core では次の環境変数が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="28186-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="28186-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="28186-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="28186-110">`ASPNETCORE_ENVIRONMENT` (<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> が呼び出されたとき)。</span><span class="sxs-lookup"><span data-stu-id="28186-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="28186-111">既定の ASP.NET Core Web アプリ テンプレートでは、`ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="28186-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="28186-112">`ASPNETCORE_ENVIRONMENT` の値によって `DOTNET_ENVIRONMENT` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="28186-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="28186-113">`IHostEnvironment.EnvironmentName` は任意の値に設定できますが、次の値はフレームワークによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="28186-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="28186-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> は、次のとおりです。[launchSettings.json](#lsj) ファイルによって、`ASPNETCORE_ENVIRONMENT` がローカル コンピューター上の `Development` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="28186-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> は、次のとおりです。`DOTNET_ENVIRONMENT` と `ASPNETCORE_ENVIRONMENT` が設定されていない場合は、既定値です。</span><span class="sxs-lookup"><span data-stu-id="28186-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="28186-116">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="28186-116">The following code:</span></span>

* <span data-ttu-id="28186-117">`ASPNETCORE_ENVIRONMENT` が `Development` に設定されているとき、[UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="28186-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="28186-118">`ASPNETCORE_ENVIRONMENT` の値が `Staging`、`Production`、または `Staging_2` に設定されるときに、[UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="28186-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="28186-119">`Startup.Configure` に <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を挿入します。</span><span class="sxs-lookup"><span data-stu-id="28186-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="28186-120">この方法は、環境ごとのコードの違いが最小限である少数の環境に対して、アプリが `Startup.Configure` の調整のみを必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="28186-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="28186-121">これは ASP.NET Core テンプレートによって生成されるコードに似ています。</span><span class="sxs-lookup"><span data-stu-id="28186-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="28186-122">[環境タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)によって [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) の値を使用し、要素にマークアップを追加したり、要素からマークアップを除外したりできます。</span><span class="sxs-lookup"><span data-stu-id="28186-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="28186-123">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)の [About ページ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)には前述のマークアップが含まれており、`IWebHostEnvironment.EnvironmentName` の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="28186-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="28186-124">Windows と macOS では、環境変数と値の大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="28186-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="28186-125">Linux の環境変数と値は、既定で**大文字と小文字が区別されます**。</span><span class="sxs-lookup"><span data-stu-id="28186-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="28186-126">EnvironmentsSample の作成</span><span class="sxs-lookup"><span data-stu-id="28186-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="28186-127">このドキュメントで使用されている[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)は、*EnvironmentsSample* という名前の Razor Pages プロジェクトに基づいています。</span><span class="sxs-lookup"><span data-stu-id="28186-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="28186-128">次のコードでは、*EnvironmentsSample* という名前の Web アプリが作成されて実行されます。</span><span class="sxs-lookup"><span data-stu-id="28186-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="28186-129">アプリを実行すると、次の出力の一部が表示されます。</span><span class="sxs-lookup"><span data-stu-id="28186-129">When the app runs, it displays some of the following output:</span></span>

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="28186-130">開発と launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="28186-130">Development and launchSettings.json</span></span>

<span data-ttu-id="28186-131">開発環境では、実稼働で公開すべきではない機能を有効にすることがあります。</span><span class="sxs-lookup"><span data-stu-id="28186-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="28186-132">たとえば、ASP.NET Core テンプレートは、開発環境で[開発者例外ページ](xref:fundamentals/error-handling#developer-exception-page)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="28186-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="28186-133">ローカル コンピューターの開発環境をプロジェクトの *Properties\launchSettings.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="28186-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="28186-134">*launchSettings.json* に設定されている環境値で、システム環境に設定されている値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="28186-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="28186-135">*launchSettings.json* ファイルは:</span><span class="sxs-lookup"><span data-stu-id="28186-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="28186-136">ローカルの開発用コンピューターでのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="28186-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="28186-137">配置されません。</span><span class="sxs-lookup"><span data-stu-id="28186-137">Is not deployed.</span></span>
* <span data-ttu-id="28186-138">プロファイル設定を含みます。</span><span class="sxs-lookup"><span data-stu-id="28186-138">contains profile settings.</span></span>

<span data-ttu-id="28186-139">次の JSON には、Visual Studio または `dotnet new` を使用して作成された、*EnvironmentsSample* という名前の ASP.NET Core Web プロジェクト用の *launchSettings.json* ファイルが示されています。</span><span class="sxs-lookup"><span data-stu-id="28186-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="28186-140">前のマークアップには、2 つのプロファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="28186-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="28186-141">`IIS Express`:Visual Studio からアプリを起動するときに使用される既定のプロファイルです。</span><span class="sxs-lookup"><span data-stu-id="28186-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="28186-142">`"commandName"` キーの値は `"IISExpress"` です。したがって、[IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) は Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="28186-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="28186-143">起動プロファイルは、プロジェクトか、含まれているその他のプロファイルに設定できます。</span><span class="sxs-lookup"><span data-stu-id="28186-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="28186-144">たとえば、次の図では、プロジェクト名を選択すると [Kestrel Web サーバー](xref:fundamentals/servers/kestrel)が起動します。</span><span class="sxs-lookup"><span data-stu-id="28186-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![メニューで IIS Express を起動](environments/_static/iisx2.png)
* <span data-ttu-id="28186-146">`EnvironmentsSample`:プロファイル名はプロジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="28186-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="28186-147">このプロファイルは、`dotnet run` を使ってアプリを起動するときに既定で使用されます。</span><span class="sxs-lookup"><span data-stu-id="28186-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="28186-148">`"commandName"` キーの値は `"Project"` です。したがって、[Kestrel Web サーバー](xref:fundamentals/servers/kestrel)が起動されます。</span><span class="sxs-lookup"><span data-stu-id="28186-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="28186-149">`commandName` の値によって、起動する Web サーバーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="28186-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="28186-150">`commandName` は次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="28186-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="28186-151">`IISExpress` は、次のとおりです。IIS Express が起動されます。</span><span class="sxs-lookup"><span data-stu-id="28186-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="28186-152">`IIS` は、次のとおりです。Web サーバーは起動されません。</span><span class="sxs-lookup"><span data-stu-id="28186-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="28186-153">IIS が使用可能であることが想定されています。</span><span class="sxs-lookup"><span data-stu-id="28186-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="28186-154">`Project` は、次のとおりです。Kestrel が起動されます。</span><span class="sxs-lookup"><span data-stu-id="28186-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="28186-155">Visual Studio のプロジェクト プロパティの **[デバッグ]** タブには、*launchSettings.json* ファイルを編集するための GUI が用意されています。</span><span class="sxs-lookup"><span data-stu-id="28186-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="28186-156">プロジェクト プロファイルを変更した場合、Web サーバーを再起動するまで変更は適用されません。</span><span class="sxs-lookup"><span data-stu-id="28186-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="28186-157">Kestrel がその環境に行われた変更を検出するには、先に再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="28186-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![プロジェクト プロパティ設定の環境変数](environments/_static/project-properties-debug.png)

<span data-ttu-id="28186-159">次の *launchSettings.json* ファイルには、複数のプロファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="28186-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="28186-160">次のようにプロファイルを選択できます。</span><span class="sxs-lookup"><span data-stu-id="28186-160">Profiles can be selected:</span></span>

* <span data-ttu-id="28186-161">Visual Studio の UI から。</span><span class="sxs-lookup"><span data-stu-id="28186-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="28186-162">コマンド シェルで [`dotnet run`](/dotnet/core/tools/dotnet-run) コマンドを使用し、`--launch-profile` オプションをプロファイルの名前に設定する。</span><span class="sxs-lookup"><span data-stu-id="28186-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="28186-163">"*この方法では Kestrel プロファイルのみがサポートされます。* "</span><span class="sxs-lookup"><span data-stu-id="28186-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="28186-164">*launchSettings.json* にはシークレットを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="28186-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="28186-165">ローカル開発のシークレットを格納するには、[シークレット マネージャー ツール](xref:security/app-secrets)を利用できます。</span><span class="sxs-lookup"><span data-stu-id="28186-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="28186-166">[Visual Studio Code](https://code.visualstudio.com/) を使用するとき、環境変数を *.vscode/launch.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="28186-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="28186-167">次の例では、いくつかの[ホストの構成値の環境変数](xref:fundamentals/host/web-host#host-configuration-values)が設定されています。</span><span class="sxs-lookup"><span data-stu-id="28186-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="28186-168">*.vscode/launch.json* ファイルは Visual Studio Code によってのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="28186-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="28186-169">実稼働</span><span class="sxs-lookup"><span data-stu-id="28186-169">Production</span></span>

<span data-ttu-id="28186-170">運用環境は、セキュリティ、[パフォーマンス](xref:performance/performance-best-practices)、アプリケーションの堅牢性が最大化されるように構成してください。</span><span class="sxs-lookup"><span data-stu-id="28186-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="28186-171">開発とは異なる一般的な設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="28186-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="28186-172">[キャッシュ](xref:performance/caching/memory)。</span><span class="sxs-lookup"><span data-stu-id="28186-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="28186-173">クライアント側のリソースはバンドルされ、小さくされ、場合によっては CDN からサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="28186-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="28186-174">診断エラー ページが無効。</span><span class="sxs-lookup"><span data-stu-id="28186-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="28186-175">フレンドリ エラー ページが有効。</span><span class="sxs-lookup"><span data-stu-id="28186-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="28186-176">実稼働の[ログ記録](xref:fundamentals/logging/index)と監視が有効。</span><span class="sxs-lookup"><span data-stu-id="28186-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="28186-177">たとえば、[Application Insights](/azure/application-insights/app-insights-asp-net-core) を使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="28186-178">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="28186-178">Set the environment</span></span>

<span data-ttu-id="28186-179">環境変数またはプラットフォームの設定を使用してテスト用の特定の環境を設定すると、便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="28186-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="28186-180">環境を設定しない場合、既定で `Production` に設定されます。ほとんどのデバッグ機能が無効になります。</span><span class="sxs-lookup"><span data-stu-id="28186-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="28186-181">環境を設定する手法は、オペレーティング システムによって異なります。</span><span class="sxs-lookup"><span data-stu-id="28186-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="28186-182">ホストが構築されると、アプリによって読み取られた最後の環境設定によってアプリの環境が決まります。</span><span class="sxs-lookup"><span data-stu-id="28186-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="28186-183">アプリの実行中にアプリの環境を変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="28186-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="28186-184">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)の [About ページ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)には、`IWebHostEnvironment.EnvironmentName` の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="28186-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="28186-185">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="28186-185">Azure App Service</span></span>

<span data-ttu-id="28186-186">[Azure App Service](https://azure.microsoft.com/services/app-service/) で環境を設定するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="28186-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="28186-187">**[App Services]** ブレードからアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="28186-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="28186-188">**[設定]** グループで、 **[構成]** ブレードを選択します。</span><span class="sxs-lookup"><span data-stu-id="28186-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="28186-189">**[アプリケーション設定]** タブで、 **[新しいアプリケーション設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="28186-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="28186-190">**[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウで、 **[名前]** に `ASPNETCORE_ENVIRONMENT` を指定します。</span><span class="sxs-lookup"><span data-stu-id="28186-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="28186-191">**[値]** には、環境を指定します (`Staging` など)。</span><span class="sxs-lookup"><span data-stu-id="28186-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="28186-192">デプロイ スロットがスワップされるときに、現在のスロットに環境設定を残したい場合は、 **[デプロイ スロットの設定]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="28186-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="28186-193">詳細については、Azure ドキュメントの「[Azure App Service でステージング環境を設定する](/azure/app-service/web-sites-staged-publishing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="28186-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="28186-194">**[OK]** を選択して、 **[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="28186-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="28186-195">**[構成]** ブレードの上部にある **[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="28186-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="28186-196">Azure portal でアプリ設定の追加、変更、削除を行うと、Azure App Service によってアプリが自動的に再起動されます。</span><span class="sxs-lookup"><span data-stu-id="28186-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="28186-197">Windows</span><span class="sxs-lookup"><span data-stu-id="28186-197">Windows</span></span>

<span data-ttu-id="28186-198">*launchSettings.json* の環境値によって、システム環境に設定されている値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="28186-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="28186-199">[dotnet run](/dotnet/core/tools/dotnet-run) を使用してアプリを起動するときに現在のセッションに `ASPNETCORE_ENVIRONMENT` を設定するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="28186-200">**コマンド プロンプト**</span><span class="sxs-lookup"><span data-stu-id="28186-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="28186-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="28186-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="28186-202">上記のコマンドでは、そのコマンド ウィンドウから起動されたプロセスに対してのみ `ASPNETCORE_ENVIRONMENT` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="28186-203">Windows でグローバルな値を設定するには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="28186-204">**[コントロール パネル]** > **[システム]** > **[システムの詳細設定]** の順に開き、`ASPNETCORE_ENVIRONMENT` 値を追加または編集します。</span><span class="sxs-lookup"><span data-stu-id="28186-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![システムの詳細プロパティ](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 環境変数](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="28186-207">管理コマンド プロンプトを開き、`setx` コマンドを使用するか、または管理用の PowerShell コマンド プロンプトを開いて、`[Environment]::SetEnvironmentVariable` を使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="28186-208">**コマンド プロンプト**</span><span class="sxs-lookup"><span data-stu-id="28186-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="28186-209">`/M` スイッチは、システム レベルで環境変数を設定することを示します。</span><span class="sxs-lookup"><span data-stu-id="28186-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="28186-210">`/M` スイッチが使用されない場合、ユーザー アカウントに対する環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="28186-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="28186-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="28186-212">`Machine` オプションの値は、システム レベルで環境変数を設定することを示します。</span><span class="sxs-lookup"><span data-stu-id="28186-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="28186-213">オプションの値が `User` に変更されると、ユーザー アカウントに対する環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="28186-214">グローバルに設定されている `ASPNETCORE_ENVIRONMENT` の環境変数は、値の設定後に開いているすべてのコマンド ウィンドウで `dotnet run` に対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="28186-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="28186-215">*launchSettings.json* の環境値によって、システム環境に設定されている値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="28186-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="28186-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="28186-216">**web.config**</span></span>

<span data-ttu-id="28186-217">`ASPNETCORE_ENVIRONMENT` 環境変数を *web.config* で設定するには、<xref:host-and-deploy/aspnet-core-module#setting-environment-variables>の「*環境変数の設定*」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="28186-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="28186-218">**プロジェクト ファイルまたは発行プロファイル**</span><span class="sxs-lookup"><span data-stu-id="28186-218">**Project file or publish profile**</span></span>

<span data-ttu-id="28186-219">**Windows IIS の配置:** [発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="28186-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="28186-220">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="28186-221">**IIS アプリケーション プール**</span><span class="sxs-lookup"><span data-stu-id="28186-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="28186-222">分離されたアプリケーション プール (IIS 10.0 以降でサポートされています) で実行するアプリに対して `ASPNETCORE_ENVIRONMENT` 環境変数を設定するには、[環境変数 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="28186-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="28186-223">`ASPNETCORE_ENVIRONMENT` 環境変数がアプリ プールで設定されている場合、その値は、システム レベルの設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="28186-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="28186-224">IIS でアプリをホストして `ASPNETCORE_ENVIRONMENT` 環境変数を追加または変更するときは、次のいずれかの方法を使用してアプリで選択された新しい値を設定してください。</span><span class="sxs-lookup"><span data-stu-id="28186-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="28186-225">コマンド プロンプトで `net stop was /y` を実行した後、`net start w3svc` を実行します。</span><span class="sxs-lookup"><span data-stu-id="28186-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="28186-226">サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="28186-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="28186-227">macOS</span><span class="sxs-lookup"><span data-stu-id="28186-227">macOS</span></span>

<span data-ttu-id="28186-228">macOS の現在の環境は、アプリ実行時にインラインで設定できます。</span><span class="sxs-lookup"><span data-stu-id="28186-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="28186-229">あるいは、アプリを実行する前に `export` で環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="28186-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="28186-230">コンピューター レベルの環境変数は *.bashrc* または *.bash_profile* ファイルに設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="28186-231">任意のテキスト エディターを利用し、ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="28186-231">Edit the file using any text editor.</span></span> <span data-ttu-id="28186-232">次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="28186-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="28186-233">Linux</span><span class="sxs-lookup"><span data-stu-id="28186-233">Linux</span></span>

<span data-ttu-id="28186-234">Linux ディストリビューションの場合、セッション ベースの変数設定にはコマンド プロンプトで `export` コマンドを使用し、コンピューター レベルの環境設定には *bash_profile* ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="28186-235">コードで環境を設定する</span><span class="sxs-lookup"><span data-stu-id="28186-235">Set the environment in code</span></span>

<span data-ttu-id="28186-236">ホストのビルド時に <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="28186-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="28186-237">以下を参照してください。<xref:fundamentals/host/generic-host#environmentname></span><span class="sxs-lookup"><span data-stu-id="28186-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="28186-238">環境別の構成</span><span class="sxs-lookup"><span data-stu-id="28186-238">Configuration by environment</span></span>

<span data-ttu-id="28186-239">環境ごとに構成を読み込む方法については、<xref:fundamentals/configuration/index#json-configuration-provider> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="28186-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="28186-240">環境別の起動のクラスとメソッド</span><span class="sxs-lookup"><span data-stu-id="28186-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="28186-241">IWebHostEnvironment を Startup クラスに挿入する</span><span class="sxs-lookup"><span data-stu-id="28186-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="28186-242"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を `Startup` コンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="28186-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="28186-243">この方法は、環境ごとのコードの違いが最小限である少数の環境に対してのみ、アプリが `Startup` の構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="28186-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="28186-244">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="28186-244">In the following example:</span></span>

* <span data-ttu-id="28186-245">環境は `_env` フィールドに保持されます。</span><span class="sxs-lookup"><span data-stu-id="28186-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="28186-246">`_env` は、アプリの環境に基づいてスタートアップ構成を適用するために `ConfigureServices` および `Configure` で使用されます。</span><span class="sxs-lookup"><span data-stu-id="28186-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="28186-247">Startup クラスの規約</span><span class="sxs-lookup"><span data-stu-id="28186-247">Startup class conventions</span></span>

<span data-ttu-id="28186-248">ASP.NET Core アプリの起動時、[Startup クラス](xref:fundamentals/startup)がアプリをブートストラップします。</span><span class="sxs-lookup"><span data-stu-id="28186-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="28186-249">アプリでは、異なる環境に対して複数の `Startup` クラスを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="28186-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="28186-250">実行時に適切な `Startup` クラスが選択されます。</span><span class="sxs-lookup"><span data-stu-id="28186-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="28186-251">名前のサフィックスが現在の環境と一致するクラスが優先されます。</span><span class="sxs-lookup"><span data-stu-id="28186-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="28186-252">一致する `Startup{EnvironmentName}` クラスが見つからない場合、`Startup` クラスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="28186-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="28186-253">この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="28186-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="28186-254">一般的なアプリでは、この方法は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="28186-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="28186-255">環境ベースの `Startup` クラスを実装するには、`Startup{EnvironmentName}` クラスとフォールバックの `Startup` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="28186-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="28186-256">アセンブリ名を受け入れる [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) オーバーロードを使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="28186-257">Startup メソッドの規約</span><span class="sxs-lookup"><span data-stu-id="28186-257">Startup method conventions</span></span>

<span data-ttu-id="28186-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) と [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) は、フォーム `Configure<EnvironmentName>` と `Configure<EnvironmentName>Services` の環境固有バージョンに対応しています。</span><span class="sxs-lookup"><span data-stu-id="28186-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="28186-259">この方法は、環境ごとにコードが大きく異なるいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="28186-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="28186-260">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="28186-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="28186-261">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="28186-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="28186-262">ASP.NET Core は、環境変数を利用し、ランタイム環境に基づいてアプリの動作を構成します。</span><span class="sxs-lookup"><span data-stu-id="28186-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="28186-263">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="28186-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="28186-264">環境</span><span class="sxs-lookup"><span data-stu-id="28186-264">Environments</span></span>

<span data-ttu-id="28186-265">ASP.NET Core はアプリの起動時に環境変数 `ASPNETCORE_ENVIRONMENT` を読み込み、その値を [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName) に格納します。</span><span class="sxs-lookup"><span data-stu-id="28186-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="28186-266">`ASPNETCORE_ENVIRONMENT` は任意の値に設定できますが、次の 3 つの値がフレームワークによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="28186-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="28186-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (既定値)</span><span class="sxs-lookup"><span data-stu-id="28186-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="28186-268">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="28186-268">The preceding code:</span></span>

* <span data-ttu-id="28186-269">`ASPNETCORE_ENVIRONMENT` が `Development` に設定されているとき、[UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="28186-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="28186-270">`ASPNETCORE_ENVIRONMENT` の値が次のいずれかに設定されているとき、[UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="28186-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="28186-271">[環境タグ ヘルパー ](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) は `IHostingEnvironment.EnvironmentName` の値を使用し、要素にマークアップを追加するか、要素からマークアップを除外します。</span><span class="sxs-lookup"><span data-stu-id="28186-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="28186-272">Windows と macOS では、環境変数と値の大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="28186-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="28186-273">Linux の環境変数と値は、既定で大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="28186-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="28186-274">開発</span><span class="sxs-lookup"><span data-stu-id="28186-274">Development</span></span>

<span data-ttu-id="28186-275">開発環境では、実稼働で公開すべきではない機能を有効にすることがあります。</span><span class="sxs-lookup"><span data-stu-id="28186-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="28186-276">たとえば、ASP.NET Core テンプレートは、開発環境で[開発者例外ページ](xref:fundamentals/error-handling#developer-exception-page)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="28186-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="28186-277">ローカル コンピューターの開発環境をプロジェクトの *Properties\launchSettings.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="28186-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="28186-278">*launchSettings.json* に設定されている環境値で、システム環境に設定されている値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="28186-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="28186-279">次の JSON では、*launchSettings.json* ファイルの 3 つのプロファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="28186-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="28186-280">*launchSettings.json* の `applicationUrl` プロパティでは、サーバー URL の一覧を指定できます。</span><span class="sxs-lookup"><span data-stu-id="28186-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="28186-281">一覧の URL 間には、セミコロンを次のように使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-281">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="28186-282">アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動すると、`"commandName": "Project"` を含む最初のプロファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="28186-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="28186-283">`commandName` の値により、起動する Web サーバーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="28186-284">`commandName` は次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="28186-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="28186-285">`Project` (Kestrel を起動する)</span><span class="sxs-lookup"><span data-stu-id="28186-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="28186-286">アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動されるとき:</span><span class="sxs-lookup"><span data-stu-id="28186-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="28186-287">利用できる場合、*launchSettings.json* が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="28186-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="28186-288">*launchSettings.json* の `environmentVariables` 設定により環境変数がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="28186-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="28186-289">ホスティング環境が表示されます。</span><span class="sxs-lookup"><span data-stu-id="28186-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="28186-290">次の出力には、[dotnet run](/dotnet/core/tools/dotnet-run) で起動したアプリが表示されます。</span><span class="sxs-lookup"><span data-stu-id="28186-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="28186-291">Visual Studio のプロジェクト プロパティの **[デバッグ]** タブには、*launchSettings.json* ファイルを編集するための GUI があります。</span><span class="sxs-lookup"><span data-stu-id="28186-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![プロジェクト プロパティ設定の環境変数](environments/_static/project-properties-debug.png)

<span data-ttu-id="28186-293">プロジェクト プロファイルを変更した場合、Web サーバーを再起動するまで変更は適用されません。</span><span class="sxs-lookup"><span data-stu-id="28186-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="28186-294">Kestrel がその環境に行われた変更を検出するには、先に再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="28186-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="28186-295">*launchSettings.json* にはシークレットを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="28186-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="28186-296">ローカル開発のシークレットを格納するには、[シークレット マネージャー ツール](xref:security/app-secrets)を利用できます。</span><span class="sxs-lookup"><span data-stu-id="28186-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="28186-297">[Visual Studio Code](https://code.visualstudio.com/) を使用するとき、環境変数を *.vscode/launch.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="28186-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="28186-298">次の例では、環境が `Development` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-298">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="28186-299">*Properties/launchSettings.json* と同じように `dotnet run` でアプリを起動すると、プロジェクトの *.vscode/launch.json* ファイルは読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="28186-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="28186-300">*launchSettings.json* ファイルがない開発中アプリを起動するとき、環境変数で環境を設定するか、コマンドライン引数を `dotnet run` コマンドに設定します。</span><span class="sxs-lookup"><span data-stu-id="28186-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="28186-301">実稼働</span><span class="sxs-lookup"><span data-stu-id="28186-301">Production</span></span>

<span data-ttu-id="28186-302">実稼働環境は、セキュリティ、パフォーマンス、アプリの堅牢性が最大化されるように構成してください。</span><span class="sxs-lookup"><span data-stu-id="28186-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="28186-303">開発とは異なる一般的な設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="28186-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="28186-304">キャッシュ。</span><span class="sxs-lookup"><span data-stu-id="28186-304">Caching.</span></span>
* <span data-ttu-id="28186-305">クライアント側のリソースはバンドルされ、小さくされ、場合によっては CDN からサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="28186-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="28186-306">診断エラー ページが無効。</span><span class="sxs-lookup"><span data-stu-id="28186-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="28186-307">フレンドリ エラー ページが有効。</span><span class="sxs-lookup"><span data-stu-id="28186-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="28186-308">実稼働のログ記録と監視が有効。</span><span class="sxs-lookup"><span data-stu-id="28186-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="28186-309">[Application Insights](/azure/application-insights/app-insights-asp-net-core) など。</span><span class="sxs-lookup"><span data-stu-id="28186-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="28186-310">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="28186-310">Set the environment</span></span>

<span data-ttu-id="28186-311">環境変数またはプラットフォームの設定を使用してテスト用の特定の環境を設定すると、便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="28186-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="28186-312">環境を設定しない場合、既定で `Production` に設定されます。ほとんどのデバッグ機能が無効になります。</span><span class="sxs-lookup"><span data-stu-id="28186-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="28186-313">環境を設定する手法は、オペレーティング システムによって異なります。</span><span class="sxs-lookup"><span data-stu-id="28186-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="28186-314">ホストが構築されると、アプリによって読み取られた最後の環境設定によってアプリの環境が決まります。</span><span class="sxs-lookup"><span data-stu-id="28186-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="28186-315">アプリの実行中にアプリの環境を変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="28186-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="28186-316">環境変数またはプラットフォームの設定</span><span class="sxs-lookup"><span data-stu-id="28186-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="28186-317">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="28186-317">Azure App Service</span></span>

<span data-ttu-id="28186-318">[Azure App Service](https://azure.microsoft.com/services/app-service/) で環境を設定するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="28186-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="28186-319">**[App Services]** ブレードからアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="28186-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="28186-320">**[設定]** グループで、 **[構成]** ブレードを選択します。</span><span class="sxs-lookup"><span data-stu-id="28186-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="28186-321">**[アプリケーション設定]** タブで、 **[新しいアプリケーション設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="28186-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="28186-322">**[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウで、 **[名前]** に `ASPNETCORE_ENVIRONMENT` を指定します。</span><span class="sxs-lookup"><span data-stu-id="28186-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="28186-323">**[値]** には、環境を指定します (`Staging` など)。</span><span class="sxs-lookup"><span data-stu-id="28186-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="28186-324">デプロイ スロットがスワップされるときに、現在のスロットに環境設定を残したい場合は、 **[デプロイ スロットの設定]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="28186-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="28186-325">詳細については、Azure ドキュメントの「[Azure App Service でステージング環境を設定する](/azure/app-service/web-sites-staged-publishing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="28186-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="28186-326">**[OK]** を選択して、 **[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="28186-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="28186-327">**[構成]** ブレードの上部にある **[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="28186-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="28186-328">Azure App Service は、アプリ設定 (環境変数) が Azure Portal で追加、変更、削除された後、アプリを自動的に再起動します。</span><span class="sxs-lookup"><span data-stu-id="28186-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="28186-329">Windows</span><span class="sxs-lookup"><span data-stu-id="28186-329">Windows</span></span>

<span data-ttu-id="28186-330">[dotnet run](/dotnet/core/tools/dotnet-run) を使用してアプリを起動するときに現在のセッションに `ASPNETCORE_ENVIRONMENT` を設定するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="28186-331">**コマンド プロンプト**</span><span class="sxs-lookup"><span data-stu-id="28186-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="28186-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="28186-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="28186-333">これらのコマンドは、現在のウィンドウでのみ有効になります。</span><span class="sxs-lookup"><span data-stu-id="28186-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="28186-334">ウィンドウが閉じられると、`ASPNETCORE_ENVIRONMENT` 設定が初期設定またはコンピューター値に戻ります。</span><span class="sxs-lookup"><span data-stu-id="28186-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="28186-335">Windows でグローバルな値を設定するには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="28186-336">**[コントロール パネル]** > **[システム]** > **[システムの詳細設定]** の順に開き、`ASPNETCORE_ENVIRONMENT` 値を追加または編集します。</span><span class="sxs-lookup"><span data-stu-id="28186-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![システムの詳細プロパティ](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 環境変数](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="28186-339">管理コマンド プロンプトを開き、`setx` コマンドを使用するか、または管理用の PowerShell コマンド プロンプトを開いて、`[Environment]::SetEnvironmentVariable` を使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="28186-340">**コマンド プロンプト**</span><span class="sxs-lookup"><span data-stu-id="28186-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="28186-341">`/M` スイッチは、システム レベルで環境変数を設定することを示します。</span><span class="sxs-lookup"><span data-stu-id="28186-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="28186-342">`/M` スイッチが使用されない場合、ユーザー アカウントに対する環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="28186-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="28186-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="28186-344">`Machine` オプションの値は、システム レベルで環境変数を設定することを示します。</span><span class="sxs-lookup"><span data-stu-id="28186-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="28186-345">オプションの値が `User` に変更されると、ユーザー アカウントに対する環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="28186-346">グローバルに設定されている `ASPNETCORE_ENVIRONMENT` の環境変数は、値の設定後に開いているすべてのコマンド ウィンドウで `dotnet run` に対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="28186-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="28186-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="28186-347">**web.config**</span></span>

<span data-ttu-id="28186-348">`ASPNETCORE_ENVIRONMENT` 環境変数を *web.config* で設定するには、<xref:host-and-deploy/aspnet-core-module#setting-environment-variables>の「*環境変数の設定*」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="28186-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="28186-349">**プロジェクト ファイルまたは発行プロファイル**</span><span class="sxs-lookup"><span data-stu-id="28186-349">**Project file or publish profile**</span></span>

<span data-ttu-id="28186-350">**Windows IIS の配置:** [発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="28186-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="28186-351">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="28186-352">**IIS アプリケーション プール**</span><span class="sxs-lookup"><span data-stu-id="28186-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="28186-353">分離されたアプリケーション プール (IIS 10.0 以降でサポートされています) で実行するアプリに対して `ASPNETCORE_ENVIRONMENT` 環境変数を設定するには、[環境変数 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="28186-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="28186-354">`ASPNETCORE_ENVIRONMENT` 環境変数がアプリ プールで設定されている場合、その値は、システム レベルの設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="28186-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="28186-355">IIS でアプリをホストして `ASPNETCORE_ENVIRONMENT` 環境変数を追加または変更するときは、次のいずれかの方法を使用してアプリで選択された新しい値を設定してください。</span><span class="sxs-lookup"><span data-stu-id="28186-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="28186-356">コマンド プロンプトで `net stop was /y` を実行した後、`net start w3svc` を実行します。</span><span class="sxs-lookup"><span data-stu-id="28186-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="28186-357">サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="28186-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="28186-358">macOS</span><span class="sxs-lookup"><span data-stu-id="28186-358">macOS</span></span>

<span data-ttu-id="28186-359">macOS の現在の環境は、アプリ実行時にインラインで設定できます。</span><span class="sxs-lookup"><span data-stu-id="28186-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="28186-360">あるいは、アプリを実行する前に `export` で環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="28186-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="28186-361">コンピューター レベルの環境変数は *.bashrc* または *.bash_profile* ファイルに設定されます。</span><span class="sxs-lookup"><span data-stu-id="28186-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="28186-362">任意のテキスト エディターを利用し、ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="28186-362">Edit the file using any text editor.</span></span> <span data-ttu-id="28186-363">次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="28186-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="28186-364">Linux</span><span class="sxs-lookup"><span data-stu-id="28186-364">Linux</span></span>

<span data-ttu-id="28186-365">Linux ディストリビューションの場合、セッション ベースの変数設定にはコマンド プロンプトで `export` コマンドを使用し、コンピューター レベルの環境設定には *bash_profile* ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="28186-366">コードで環境を設定する</span><span class="sxs-lookup"><span data-stu-id="28186-366">Set the environment in code</span></span>

<span data-ttu-id="28186-367">ホストのビルド時に <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="28186-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="28186-368">以下を参照してください。<xref:fundamentals/host/web-host#environment></span><span class="sxs-lookup"><span data-stu-id="28186-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="28186-369">環境別の構成</span><span class="sxs-lookup"><span data-stu-id="28186-369">Configuration by environment</span></span>

<span data-ttu-id="28186-370">環境ごとに構成を読み込む場合の推奨事項は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="28186-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="28186-371">*appsettings* ファイル (*appsettings.{Environment}.json*)。</span><span class="sxs-lookup"><span data-stu-id="28186-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="28186-372">以下を参照してください。<xref:fundamentals/configuration/index#json-configuration-provider></span><span class="sxs-lookup"><span data-stu-id="28186-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="28186-373">環境変数 (アプリがホストされている各システムで設定します)。</span><span class="sxs-lookup"><span data-stu-id="28186-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="28186-374">「 <xref:fundamentals/host/web-host#environment> 」および「 <xref:security/app-secrets#environment-variables>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="28186-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="28186-375">Secret Manager (開発環境の場合のみ)</span><span class="sxs-lookup"><span data-stu-id="28186-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="28186-376">以下を参照してください。<xref:security/app-secrets></span><span class="sxs-lookup"><span data-stu-id="28186-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="28186-377">環境別の起動のクラスとメソッド</span><span class="sxs-lookup"><span data-stu-id="28186-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="28186-378">IHostingEnvironment を Startup.Configure に挿入する</span><span class="sxs-lookup"><span data-stu-id="28186-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="28186-379"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> を `Startup.Configure` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="28186-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="28186-380">この方法は、環境ごとのコードの違いが最小限である少数の環境に対してのみ、アプリが `Startup.Configure` の構成だけを必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="28186-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="28186-381">IHostingEnvironment を Startup クラスに挿入する</span><span class="sxs-lookup"><span data-stu-id="28186-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="28186-382">`Startup` コンストラクターに <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> を挿入し、`Startup` クラス全体で使用するフィールドにサービスを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="28186-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="28186-383">この方法は、環境ごとのコードの違いが最小限である少数の環境に対してのみ、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="28186-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="28186-384">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="28186-384">In the following example:</span></span>

* <span data-ttu-id="28186-385">環境は `_env` フィールドに保持されます。</span><span class="sxs-lookup"><span data-stu-id="28186-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="28186-386">`_env` は、アプリの環境に基づいてスタートアップ構成を適用するために `ConfigureServices` および `Configure` で使用されます。</span><span class="sxs-lookup"><span data-stu-id="28186-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="28186-387">Startup クラスの規約</span><span class="sxs-lookup"><span data-stu-id="28186-387">Startup class conventions</span></span>

<span data-ttu-id="28186-388">ASP.NET Core アプリの起動時、[Startup クラス](xref:fundamentals/startup)がアプリをブートストラップします。</span><span class="sxs-lookup"><span data-stu-id="28186-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="28186-389">アプリでは、環境 (たとえば `StartupDevelopment`) ごとに個別の `Startup` クラスを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="28186-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="28186-390">実行時に適切な `Startup` クラスが選択されます。</span><span class="sxs-lookup"><span data-stu-id="28186-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="28186-391">名前のサフィックスが現在の環境と一致するクラスが優先されます。</span><span class="sxs-lookup"><span data-stu-id="28186-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="28186-392">一致する `Startup{EnvironmentName}` クラスが見つからない場合、`Startup` クラスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="28186-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="28186-393">この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="28186-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="28186-394">環境ベースの `Startup` クラスを実装するには、使用中の各環境に対して `Startup{EnvironmentName}` クラスと、フォールバックの `Startup` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="28186-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="28186-395">アセンブリ名を受け入れる [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) オーバーロードを使用します。</span><span class="sxs-lookup"><span data-stu-id="28186-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="28186-396">Startup メソッドの規約</span><span class="sxs-lookup"><span data-stu-id="28186-396">Startup method conventions</span></span>

<span data-ttu-id="28186-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) と [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) は、フォーム `Configure<EnvironmentName>` と `Configure<EnvironmentName>Services` の環境固有バージョンに対応しています。</span><span class="sxs-lookup"><span data-stu-id="28186-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="28186-398">この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="28186-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="28186-399">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="28186-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
