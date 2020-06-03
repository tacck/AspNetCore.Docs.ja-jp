---
<span data-ttu-id="cefdc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-102">'Blazor'</span></span>
- <span data-ttu-id="cefdc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-103">'Identity'</span></span>
- <span data-ttu-id="cefdc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-105">'Razor'</span></span>
- <span data-ttu-id="cefdc-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-106">'SignalR' uid:</span></span> 

---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="cefdc-107">ASP.NET Core アプリを配置するための Visual Studio 発行プロファイル (.pubxml)</span><span class="sxs-lookup"><span data-stu-id="cefdc-107">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="cefdc-108">作成者: [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cefdc-108">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cefdc-109">このドキュメントでは、Visual Studio 2019 以降を使用して、発行プロファイルを作成および使用する方法に焦点を当てます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-109">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="cefdc-110">Visual Studio を使用して作成した発行プロファイルは、MSBuild および Visual Studio で使用することができます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-110">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="cefdc-111">Azure への発行の手順については、<xref:tutorials/publish-to-azure-webapp-using-vs> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-111">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="cefdc-112">`dotnet new mvc` コマンドでは、次のルート レベルの [\<Project> 要素](/visualstudio/msbuild/project-element-msbuild)を含むプロジェクト ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-112">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="cefdc-113">上の `<Project>` 要素の `Sdk` 属性では、MSBuild の[プロパティ](/visualstudio/msbuild/msbuild-properties)と[ターゲット](/visualstudio/msbuild/msbuild-targets)が、それぞれ *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* と *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* からインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-113">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="cefdc-114">(Visual Studio 2019 Enterprise の場合) `$(MSBuildSDKsPath)` の既定の場所は、 *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="cefdc-114">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="cefdc-115">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) は、`Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) や `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)) などの他の SDK に依存します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-115">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="cefdc-116">依存する各 SDK に関連付けられている MSBuild のプロパティとターゲットがインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-116">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="cefdc-117">発行ターゲットでは、使われる発行方法に基づいて、適切なターゲットのセットがインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-117">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="cefdc-118">MSBuild または Visual Studio がプロジェクトを読み込むと、次の高レベルのアクションが発生します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-118">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="cefdc-119">プロジェクトをビルドする</span><span class="sxs-lookup"><span data-stu-id="cefdc-119">Build project</span></span>
* <span data-ttu-id="cefdc-120">発行するファイルを計算する</span><span class="sxs-lookup"><span data-stu-id="cefdc-120">Compute files to publish</span></span>
* <span data-ttu-id="cefdc-121">ターゲットにファイルを発行する</span><span class="sxs-lookup"><span data-stu-id="cefdc-121">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="cefdc-122">プロジェクト項目を比較する</span><span class="sxs-lookup"><span data-stu-id="cefdc-122">Compute project items</span></span>

<span data-ttu-id="cefdc-123">プロジェクトが読み込まれると、[MSBuild プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items) (ファイル) が計算されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-123">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="cefdc-124">項目の種類によって、ファイルの処理方法が決まります。</span><span class="sxs-lookup"><span data-stu-id="cefdc-124">The item type determines how the file is processed.</span></span> <span data-ttu-id="cefdc-125">既定で、 *.cs* ファイルは `Compile` 項目一覧に含まれています。</span><span class="sxs-lookup"><span data-stu-id="cefdc-125">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="cefdc-126">`Compile` 項目一覧のファイルがコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-126">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="cefdc-127">`Content` 項目一覧には、ビルドの出力に加え、発行されるファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cefdc-127">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="cefdc-128">既定では、`wwwroot\**`、`**\*.config`、`**\*.json` というパターンに一致するファイルが、`Content` の項目一覧に含まれます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-128">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="cefdc-129">たとえば、`wwwroot\**` [glob パターン](https://gruntjs.com/configuring-tasks#globbing-patterns)は、*wwwroot* フォルダーとそのサブフォルダー内のすべてのファイルと一致します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-129">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cefdc-130">[Web SDK](xref:razor-pages/web-sdk) では、[Razor SDK](xref:razor-pages/sdk) がインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-130">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="cefdc-131">その結果、`**\*.cshtml` および `**\*.razor` というパターンに一致するファイルが、`Content` の項目一覧に含まれます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-131">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="cefdc-132">[Web SDK](xref:razor-pages/web-sdk) では、[Razor SDK](xref:razor-pages/sdk) がインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-132">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="cefdc-133">その結果、`**\*.cshtml` というパターンに一致するファイルが、`Content` の項目一覧に含まれます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-133">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="cefdc-134">発行一覧に明示的にファイルを追加するには、「[ファイルを含める](#include-files)」セクションで説明されているように、 *.csproj* ファイルに直接ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-134">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="cefdc-135">Visual Studio で **[発行]** ボタンを選択するか、コマンド ラインから発行すると、以下が実行されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-135">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="cefdc-136">プロパティ/項目が計算されます (ビルドに必要なファイル)。</span><span class="sxs-lookup"><span data-stu-id="cefdc-136">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="cefdc-137">**Visual Studio のみ**: NuGet パッケージが復元されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-137">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="cefdc-138">(CLI では、ユーザーが明示的に復元する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="cefdc-138">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="cefdc-139">プロジェクトがビルドされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-139">The project builds.</span></span>
* <span data-ttu-id="cefdc-140">発行項目が計算されます (発行に必要なファイル)。</span><span class="sxs-lookup"><span data-stu-id="cefdc-140">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="cefdc-141">プロジェクトが発行されます (計算されたファイルが発行先にコピーされます)。</span><span class="sxs-lookup"><span data-stu-id="cefdc-141">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="cefdc-142">ASP.NET Core プロジェクトは、プロジェクト ファイルの `Microsoft.NET.Sdk.Web` を参照して、*app_offline.htm* ファイルを Web アプリのディレクトリのルートに配置します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-142">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="cefdc-143">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-143">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="cefdc-144">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-144">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="cefdc-145">基本的なコマンド ラインからの発行</span><span class="sxs-lookup"><span data-stu-id="cefdc-145">Basic command-line publishing</span></span>

<span data-ttu-id="cefdc-146">コマンド ラインからの発行は、.NET Core をサポートするすべてのプラットフォームで機能し、Visual Studio は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="cefdc-146">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="cefdc-147">次の例では、プロジェクト ディレクトリ ( *.csproj* ファイルが含まれているディレクトリ) から .NET Core CLI の [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-147">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="cefdc-148">プロジェクト フォルダーが現在の作業ディレクトリでない場合は、プロジェクト ファイルのパスで明示的に渡します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-148">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="cefdc-149">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-149">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="cefdc-150">Web アプリを作成して発行するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-150">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="cefdc-151">`dotnet publish` コマンドでは次のような出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-151">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="cefdc-152">既定の発行フォルダーの形式は *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\* です。</span><span class="sxs-lookup"><span data-stu-id="cefdc-152">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="cefdc-153">たとえば、*bin\Debug\netcoreapp2.2\publish\\* などです。</span><span class="sxs-lookup"><span data-stu-id="cefdc-153">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="cefdc-154">次のコマンドでは、`Release` ビルドと発行ディレクトリを指定します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-154">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="cefdc-155">`dotnet publish` コマンドは、`Publish` ターゲットを呼び出す MSBuild を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-155">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="cefdc-156">`dotnet publish` に渡されたすべてのパラメーターが MSBuild に渡されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-156">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="cefdc-157">`-c` と `-o` のパラメーターは、それぞれ MSBuild の `Configuration` と `OutputPath` にマップします。</span><span class="sxs-lookup"><span data-stu-id="cefdc-157">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="cefdc-158">MSBuild のプロパティは、次のいずれかの形式を使用して渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-158">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="cefdc-159">たとえば、次のコマンドは、`Release` ビルドをネットワーク共有に発行します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-159">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="cefdc-160">ネットワーク共有は、スラッシュを使用して指定します ( *//r8/* )。 .NET Core がサポートされるすべてのプラットフォームで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-160">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="cefdc-161">配置用に発行したアプリが実行されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-161">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="cefdc-162">アプリが実行中は、*publish* フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-162">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="cefdc-163">ロックされているファイルはコピーできないため、配置は行われません。</span><span class="sxs-lookup"><span data-stu-id="cefdc-163">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="cefdc-164">プロファイルを発行する</span><span class="sxs-lookup"><span data-stu-id="cefdc-164">Publish profiles</span></span>

<span data-ttu-id="cefdc-165">このセクションでは、Visual Studio 2019 以降を使用して発行プロファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-165">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="cefdc-166">プロファイルが作成されると、Visual Studio またはコマンド ラインから発行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="cefdc-166">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="cefdc-167">発行プロファイルは発行プロセスを簡略化でき、任意の数のプロファイルが存在できます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-167">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="cefdc-168">次のいずれかの方法で、Visual Studio で発行プロファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-168">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="cefdc-169">**ソリューション エクスプローラー**で、プロジェクトを右クリックして **[発行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="cefdc-169">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="cefdc-170">**[ビルド]** メニューの **[{PROJECT NAME} の発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-170">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="cefdc-171">アプリ機能ページの **[発行]** タブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-171">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="cefdc-172">プロジェクトに発行プロファイルが含まれていない場合は、 **[発行先を選択]** のページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-172">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="cefdc-173">次の発行先のいずれかを選択するように求められます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-173">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="cefdc-174">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cefdc-174">Azure App Service</span></span>
* <span data-ttu-id="cefdc-175">Azure App Service on Linux</span><span class="sxs-lookup"><span data-stu-id="cefdc-175">Azure App Service on Linux</span></span>
* <span data-ttu-id="cefdc-176">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="cefdc-176">Azure Virtual Machines</span></span>
* <span data-ttu-id="cefdc-177">フォルダー</span><span class="sxs-lookup"><span data-stu-id="cefdc-177">Folder</span></span>
* <span data-ttu-id="cefdc-178">(任意の Web サーバーの) IIS、FTP、Web 配置</span><span class="sxs-lookup"><span data-stu-id="cefdc-178">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="cefdc-179">インポート プロファイル</span><span class="sxs-lookup"><span data-stu-id="cefdc-179">Import Profile</span></span>

<span data-ttu-id="cefdc-180">最も適切な発行先を決定するには、[自分に合った発行オプション](/visualstudio/ide/not-in-toc/web-publish-options)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-180">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="cefdc-181">発行先を **[フォルダー]** に選択した場合は、発行された資産を保存するフォルダーのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-181">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="cefdc-182">既定のフォルダー パスは *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\* です。</span><span class="sxs-lookup"><span data-stu-id="cefdc-182">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="cefdc-183">たとえば、*bin\Release\netcoreapp2.2\publish\\* などです。</span><span class="sxs-lookup"><span data-stu-id="cefdc-183">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="cefdc-184">**[プロファイルの作成]** ボタンを選択して完了します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-184">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="cefdc-185">発行プロファイルが作成されると、 **[発行]** タブの内容が変化します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-185">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="cefdc-186">新しく作成したプロファイルがドロップダウン リストに表示されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-186">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="cefdc-187">別の新しいプロファイルを作成するには、ドロップダウン リストから **[新しいプロファイルの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-187">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="cefdc-188">Visual Studio の発行ツールでは、発行プロファイルについて説明する *Properties/PublishProfiles/{PROFILE NAME}.pubxml* という MSBuild ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-188">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="cefdc-189">*.pubxml* ファイルは:</span><span class="sxs-lookup"><span data-stu-id="cefdc-189">The *.pubxml* file:</span></span>

* <span data-ttu-id="cefdc-190">発行構成の設定を含み、発行プロセスによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-190">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="cefdc-191">変更して、ビルドと発行プロセスをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-191">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="cefdc-192">Azure ターゲットに発行する場合、 *.pubxml* ファイルには、Azure サブスクリプション識別子が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-192">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="cefdc-193">そのターゲットの種類では、このファイルをソース管理に追加することはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="cefdc-193">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="cefdc-194">Azure 以外のターゲットに発行する場合は、 *.pubxml* ファイルをチェックインするほうが安全です。</span><span class="sxs-lookup"><span data-stu-id="cefdc-194">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="cefdc-195">機微な情報 (発行パスワードなど) は、個々のユーザー/コンピューター レベルで暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-195">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="cefdc-196">それは、*Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-196">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="cefdc-197">このファイルには機微な情報が格納される可能性があるため、ソース コード管理にチェックインしないでください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-197">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="cefdc-198">ASP.NET Core で Web アプリを発行する方法の概要については、<xref:host-and-deploy/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-198">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="cefdc-199">ASP.NET Core Web アプリを発行するために必要な MSBuild タスクとターゲットは、[aspnet/websdk リポジトリ](https://github.com/aspnet/websdk)のオープン ソースです。</span><span class="sxs-lookup"><span data-stu-id="cefdc-199">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="cefdc-200">以下のコマンドでは、フォルダー、MSDeploy、および [Kudu](https://github.com/projectkudu/kudu/wiki) 発行プロファイルを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-200">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="cefdc-201">MSDeploy にはクロス プラットフォームのサポートがないため、次の MSDeploy オプションは、Windows でのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-201">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="cefdc-202">**フォルダー (クロス プラットフォームで動作します):**</span><span class="sxs-lookup"><span data-stu-id="cefdc-202">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="cefdc-203">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="cefdc-203">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="cefdc-204">**MSDeploy パッケージ:**</span><span class="sxs-lookup"><span data-stu-id="cefdc-204">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="cefdc-205">前の例では、</span><span class="sxs-lookup"><span data-stu-id="cefdc-205">In the preceding examples:</span></span>

* <span data-ttu-id="cefdc-206">`dotnet publish` と `dotnet build` により、任意のプラットフォームから Azure に発行する Kudu API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cefdc-206">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="cefdc-207">Visual Studio の発行は、Kudu API をサポートしていますが、Azure へのクロスプラットフォームの発行は WebSDK がサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cefdc-207">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="cefdc-208">`dotnet publish` コマンドに `DeployOnBuild` を渡さないでください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-208">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="cefdc-209">詳細については、「[Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-209">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="cefdc-210">次の内容の発行プロファイルを、プロジェクトの *Properties/PublishProfiles* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-210">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="cefdc-211">フォルダーの発行の例</span><span class="sxs-lookup"><span data-stu-id="cefdc-211">Folder publish example</span></span>

<span data-ttu-id="cefdc-212">*FolderProfile* という名前のプロファイルを使用して発行する場合は、次のいずれかのコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-212">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="cefdc-213">.NET Core CLI の [dotnet build](/dotnet/core/tools/dotnet-build) コマンドにより `msbuild` が呼び出されて、ビルドと発行プロセスが実行されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-213">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="cefdc-214">`dotnet build` と `msbuild` のコマンドは、フォルダー プロファイルで渡す場合は同等です。</span><span class="sxs-lookup"><span data-stu-id="cefdc-214">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="cefdc-215">Windows で `msbuild` を直接呼び出すと、MSBuild の .NET Framework バージョンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-215">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="cefdc-216">フォルダー以外のプロファイルで `dotnet build` を呼び出すと:</span><span class="sxs-lookup"><span data-stu-id="cefdc-216">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="cefdc-217">MSDeploy を使用する `msbuild` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-217">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="cefdc-218">失敗します (Windows で実行されている場合でも)。</span><span class="sxs-lookup"><span data-stu-id="cefdc-218">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="cefdc-219">フォルダー以外のプロファイルで発行するには、`msbuild` を直接呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-219">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="cefdc-220">次のフォルダー発行プロファイルは、Visual Studio で作成され、ネットワーク共有に発行されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="cefdc-221">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="cefdc-221">In the preceding example:</span></span>

* <span data-ttu-id="cefdc-222">`<ExcludeApp_Data>` プロパティは、XML スキーマの要件を満たすためだけに存在します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-222">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="cefdc-223">プロジェクトのルートに *App_Data* フォルダーがある場合でも、`<ExcludeApp_Data>` プロパティが発行プロセスに影響することはありません。</span><span class="sxs-lookup"><span data-stu-id="cefdc-223">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="cefdc-224">*App_Data* フォルダーは ASP.NET 4.x プロジェクトのように特別な処理を受信しません。</span><span class="sxs-lookup"><span data-stu-id="cefdc-224">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="cefdc-225">`<LastUsedBuildConfiguration>` プロパティが `Release` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="cefdc-225">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="cefdc-226">Visual Studio から発行すると、発行プロセスが開始されたときの値を使用して、`<LastUsedBuildConfiguration>` の値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-226">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="cefdc-227">`<LastUsedBuildConfiguration>` は特殊なので、インポートされる MSBuild ファイルでオーバーライドされないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-227">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="cefdc-228">ただし、このプロパティは次の方法のいずれかを使用してコマンドラインからオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-228">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="cefdc-229">.NET Core CLI の使用:</span><span class="sxs-lookup"><span data-stu-id="cefdc-229">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="cefdc-230">MSBuild の使用:</span><span class="sxs-lookup"><span data-stu-id="cefdc-230">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="cefdc-231">詳細については、「[MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)」(MSBuild: 構成プロパティの設定方法) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-231">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="cefdc-232">コマンド ラインから MSDeploy エンドポイントに発行する</span><span class="sxs-lookup"><span data-stu-id="cefdc-232">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="cefdc-233">次の例では、Visual Studio によって作成された *AzureWebApp* という名前の ASP.NET Core Web アプリが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-233">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="cefdc-234">Azure アプリ発行プロファイルは、Visual Studio を使用して追加されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-234">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="cefdc-235">プロファイルを作成する方法の詳細については、「[発行プロファイル](#publish-profiles)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-235">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="cefdc-236">発行プロファイルを使用してアプリを配置するには、Visual Studio の**開発者コマンド プロンプト**から `msbuild` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-236">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="cefdc-237">コマンド プロンプトは、Windows タスク バー上の **[スタート]** メニューの *Visual Studio* フォルダーで利用できます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-237">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="cefdc-238">簡単にアクセスできるようにするために、Visual Studio の **[ツール]** メニューにコマンド プロンプトを追加できます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-238">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="cefdc-239">詳細については、「[Visual Studio 用開発者コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-239">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="cefdc-240">MSBuild では次の構文が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-240">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="cefdc-241">{PATH}:アプリのプロジェクト ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="cefdc-241">{PATH}: Path to the app's project file.</span></span>
* <span data-ttu-id="cefdc-242">{PROFILE}:発行プロファイルの名前。</span><span class="sxs-lookup"><span data-stu-id="cefdc-242">{PROFILE}: Name of the publish profile.</span></span>
* <span data-ttu-id="cefdc-243">{USERNAME}:MSDeploy ユーザー名。</span><span class="sxs-lookup"><span data-stu-id="cefdc-243">{USERNAME}: MSDeploy username.</span></span> <span data-ttu-id="cefdc-244">{USERNAME} は発行プロファイルで確認できます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-244">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="cefdc-245">{PASSWORD}:MSDeploy パスワード。</span><span class="sxs-lookup"><span data-stu-id="cefdc-245">{PASSWORD}: MSDeploy password.</span></span> <span data-ttu-id="cefdc-246">*{PROFILE}.PublishSettings* ファイルから {PASSWORD} を取得します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-246">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="cefdc-247">次のいずれかの方法で、 *.PublishSettings* ファイルをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="cefdc-247">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="cefdc-248">**ソリューション エクスプローラー**: **[ビュー]**  >  **[Cloud Explorer]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-248">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="cefdc-249">ご自分の Azure サブスクリプションを使用して接続します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-249">Connect with your Azure subscription.</span></span> <span data-ttu-id="cefdc-250">**App Services** を開きます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-250">Open **App Services**.</span></span> <span data-ttu-id="cefdc-251">アプリを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="cefdc-251">Right-click the app.</span></span> <span data-ttu-id="cefdc-252">**[発行プロファイルのダウンロード]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-252">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="cefdc-253">Azure portal: Web アプリの **[概要]** ウィンドウで **[発行プロファイルの取得]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="cefdc-253">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="cefdc-254">次の例では、*AzureWebApp - Web Deploy* という名前の発行プロファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-254">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="cefdc-255">Windows コマンド シェルからの .NET Core CLI の [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドで、発行プロファイルを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-255">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="cefdc-256">`dotnet msbuild` コマンドはクロス プラットフォームのコマンドで、macOS および Linux 上で ASP.NET Core アプリをコンパイルすることができます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-256">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="cefdc-257">ただし、macOS および Linux 上の MSBuild では、Azure またはその他の MSDeploy エンドポイントにアプリを配置することはできません。</span><span class="sxs-lookup"><span data-stu-id="cefdc-257">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="cefdc-258">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="cefdc-258">Set the environment</span></span>

<span data-ttu-id="cefdc-259">発行プロファイル ( *.pubxml*) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを追加し、アプリの[環境](xref:fundamentals/environments)を設定します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-259">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="cefdc-260">*web.config* の変換が必要な場合 (たとえば、構成、プロファイル、または環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-260">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="cefdc-261">ファイルを除外する</span><span class="sxs-lookup"><span data-stu-id="cefdc-261">Exclude files</span></span>

<span data-ttu-id="cefdc-262">ASP.NET Core Web アプリを発行するときは、次の資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-262">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="cefdc-263">ビルド成果物</span><span class="sxs-lookup"><span data-stu-id="cefdc-263">Build artifacts</span></span>
* <span data-ttu-id="cefdc-264">次の glob パターンと一致するフォルダーおよびファイル:</span><span class="sxs-lookup"><span data-stu-id="cefdc-264">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="cefdc-265">`**\*.config` (例: *web.config*)</span><span class="sxs-lookup"><span data-stu-id="cefdc-265">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="cefdc-266">`**\*.json` (例: *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="cefdc-266">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="cefdc-267">MSBuild では、[glob パターン](https://gruntjs.com/configuring-tasks#globbing-patterns)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cefdc-267">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="cefdc-268">たとえば、次の `<Content>` 要素では、*wwwroot/content* フォルダーとそのサブフォルダー内にあるテキスト ( *.txt*) ファイルのコピーが抑制されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-268">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="cefdc-269">上記のマークアップは、発行プロファイルまたは *.csproj* ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-269">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="cefdc-270">*.csproj* ファイルに追加すると、プロジェクト内のすべての発行プロファイルにルールが追加されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-270">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="cefdc-271">次の `<MsDeploySkipRules>` 要素では、*wwwroot\content* フォルダーのすべてのファイルが除外されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-271">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="cefdc-272">`<MsDeploySkipRules>` は、"*スキップされる*" ターゲットを配置サイトから削除しません。</span><span class="sxs-lookup"><span data-stu-id="cefdc-272">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="cefdc-273">`<Content>` のターゲットであるファイルとフォルダーは、配置サイトから削除されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-273">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="cefdc-274">たとえば、配置される Web アプリに次のファイルが含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="cefdc-274">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="cefdc-275">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cefdc-275">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="cefdc-276">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cefdc-276">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="cefdc-277">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cefdc-277">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="cefdc-278">次の `<MsDeploySkipRules>` 要素を追加した場合、これらのファイルは配置サイトでは削除されません。</span><span class="sxs-lookup"><span data-stu-id="cefdc-278">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="cefdc-279">前述の `<MsDeploySkipRules>` 要素は、"*スキップされる*" ファイルが配置されないようにします。</span><span class="sxs-lookup"><span data-stu-id="cefdc-279">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="cefdc-280">それは、いったん配置されたファイルは削除しません。</span><span class="sxs-lookup"><span data-stu-id="cefdc-280">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="cefdc-281">次の `<Content>` 要素は、配置サイトのターゲット ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-281">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="cefdc-282">前述の `<Content>` 要素を使用してコマンド ライン配置を行うと、次のような出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-282">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="cefdc-283">インクルード ファイル</span><span class="sxs-lookup"><span data-stu-id="cefdc-283">Include files</span></span>

<span data-ttu-id="cefdc-284">次のセクションでは、発行時のファイル インクルードのさまざまな方法を概説します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-284">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="cefdc-285">「[一般的なファイル インクルード](#general-file-inclusion)」のセクションでは、[Web SDK](xref:razor-pages/web-sdk) の発行先ファイルから提供される `DotNetPublishFiles` 項目を使用します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-285">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="cefdc-286">「[選択的なファイル インクルード](#selective-file-inclusion)」のセクションでは、[.NET Core SDK](/dotnet/core/project-sdk/msbuild-props) の発行先ファイルから提供される `ResolvedFileToPublish` 項目を使用します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-286">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="cefdc-287">Web SDK は .NET Core SDK に依存するため、どちらの項目も ASP.NET Core プロジェクトで使用することができます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-287">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="cefdc-288">一般的なファイル インクルード</span><span class="sxs-lookup"><span data-stu-id="cefdc-288">General file inclusion</span></span>

<span data-ttu-id="cefdc-289">次の例の `<ItemGroup>` 要素は、プロジェクト ディレクトリの外部にあるフォルダーを発行済みサイトのフォルダーにコピーする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="cefdc-289">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="cefdc-290">次のマークアップの `<ItemGroup>` に追加されたすべてのファイルが既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-290">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="cefdc-291">上のマークアップでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-291">The preceding markup:</span></span>

* <span data-ttu-id="cefdc-292">*.csproj* ファイルまたは発行プロファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-292">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="cefdc-293">*.csproj* ファイルに追加した場合は、プロジェクトの各発行プロファイルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-293">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="cefdc-294">`Include` 属性の glob パターンに一致するファイルを格納するための `_CustomFiles` 項目を宣言します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-294">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="cefdc-295">パターンで参照される*イメージ* フォルダーはプロジェクト ディレクトリの外部にあります。</span><span class="sxs-lookup"><span data-stu-id="cefdc-295">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="cefdc-296">`$(MSBuildProjectDirectory)` という名前の[予約済みプロパティ](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)がプロジェクト ファイルの絶対パスを解決します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-296">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="cefdc-297">`DotNetPublishFiles` 項目にファイルの一覧を提供します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-297">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="cefdc-298">既定では、項目の `<DestinationRelativePath>` 要素は空です。</span><span class="sxs-lookup"><span data-stu-id="cefdc-298">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="cefdc-299">既定値はマークアップでオーバーライドされ、`%(RecursiveDir)` などの[既知の項目メタデータ](/visualstudio/msbuild/msbuild-well-known-item-metadata)を使用します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-299">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="cefdc-300">内部のテキストは、発行済みサイトの *wwwroot/images* フォルダーを表します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-300">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="cefdc-301">選択的なファイル インクルード</span><span class="sxs-lookup"><span data-stu-id="cefdc-301">Selective file inclusion</span></span>

<span data-ttu-id="cefdc-302">次の例の強調表示されたマークアップは、次のことを示しています。</span><span class="sxs-lookup"><span data-stu-id="cefdc-302">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="cefdc-303">プロジェクトの外部にあるファイルは、発行済みのサイトの *wwwroot* フォルダーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-303">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="cefdc-304">*ReadMe2.md* のファイル名は維持されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-304">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="cefdc-305">*wwwroot\Content* フォルダーは除外されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-305">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="cefdc-306">*Views\Home\About2.cshtml* は除外されます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-306">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="cefdc-307">上記の例では、既定の動作として `Include` 属性に提供されたファイルが常に発行済みサイトにコピーされる、`ResolvedFileToPublish` 項目を使用します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-307">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="cefdc-308">既定の動作は、内部テキストが `Never` または `PreserveNewest` の子要素 `<CopyToPublishDirectory>` を含めるとオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-308">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="cefdc-309">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-309">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="cefdc-310">他の配置例については、[Web SDK リポジトリの Readme](https://github.com/aspnet/websdk) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cefdc-310">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="cefdc-311">発行の前または後にターゲットを実行する</span><span class="sxs-lookup"><span data-stu-id="cefdc-311">Run a target before or after publishing</span></span>

<span data-ttu-id="cefdc-312">組み込みの `BeforePublish` と `AfterPublish` ターゲットは、ターゲットの発行前または後にターゲットを実行できます。</span><span class="sxs-lookup"><span data-stu-id="cefdc-312">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="cefdc-313">発行の前と後の両方でコンソール メッセージをログに記録するには、発行プロファイルに次の要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-313">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="cefdc-314">信頼されていない証明書を使用してサーバーに発行する</span><span class="sxs-lookup"><span data-stu-id="cefdc-314">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="cefdc-315">値が `True` の `<AllowUntrustedCertificate>` プロパティを発行プロファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-315">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="cefdc-316">Kudu サービス</span><span class="sxs-lookup"><span data-stu-id="cefdc-316">The Kudu service</span></span>

<span data-ttu-id="cefdc-317">Azure App Service での Web アプリのデプロイに含まれるファイルを表示するには、[Kudu サービス](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) を使用します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-317">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="cefdc-318">`scm` トークンを Web アプリ名に追加します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-318">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="cefdc-319">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-319">For example:</span></span>

| <span data-ttu-id="cefdc-320">URL</span><span class="sxs-lookup"><span data-stu-id="cefdc-320">URL</span></span>                                    | <span data-ttu-id="cefdc-321">結果</span><span class="sxs-lookup"><span data-stu-id="cefdc-321">Result</span></span>       |
| ---
<span data-ttu-id="cefdc-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-323">'Blazor'</span></span>
- <span data-ttu-id="cefdc-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-324">'Identity'</span></span>
- <span data-ttu-id="cefdc-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-326">'Razor'</span></span>
- <span data-ttu-id="cefdc-327">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-329">'Blazor'</span></span>
- <span data-ttu-id="cefdc-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-330">'Identity'</span></span>
- <span data-ttu-id="cefdc-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-332">'Razor'</span></span>
- <span data-ttu-id="cefdc-333">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-335">'Blazor'</span></span>
- <span data-ttu-id="cefdc-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-336">'Identity'</span></span>
- <span data-ttu-id="cefdc-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-338">'Razor'</span></span>
- <span data-ttu-id="cefdc-339">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-341">'Blazor'</span></span>
- <span data-ttu-id="cefdc-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-342">'Identity'</span></span>
- <span data-ttu-id="cefdc-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-344">'Razor'</span></span>
- <span data-ttu-id="cefdc-345">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-347">'Blazor'</span></span>
- <span data-ttu-id="cefdc-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-348">'Identity'</span></span>
- <span data-ttu-id="cefdc-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-350">'Razor'</span></span>
- <span data-ttu-id="cefdc-351">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-353">'Blazor'</span></span>
- <span data-ttu-id="cefdc-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-354">'Identity'</span></span>
- <span data-ttu-id="cefdc-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-356">'Razor'</span></span>
- <span data-ttu-id="cefdc-357">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-359">'Blazor'</span></span>
- <span data-ttu-id="cefdc-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-360">'Identity'</span></span>
- <span data-ttu-id="cefdc-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-362">'Razor'</span></span>
- <span data-ttu-id="cefdc-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-365">'Blazor'</span></span>
- <span data-ttu-id="cefdc-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-366">'Identity'</span></span>
- <span data-ttu-id="cefdc-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-368">'Razor'</span></span>
- <span data-ttu-id="cefdc-369">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-371">'Blazor'</span></span>
- <span data-ttu-id="cefdc-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-372">'Identity'</span></span>
- <span data-ttu-id="cefdc-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-374">'Razor'</span></span>
- <span data-ttu-id="cefdc-375">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-375">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-376">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-376">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-377">'Blazor'</span></span>
- <span data-ttu-id="cefdc-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-378">'Identity'</span></span>
- <span data-ttu-id="cefdc-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-380">'Razor'</span></span>
- <span data-ttu-id="cefdc-381">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-382">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-382">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-383">'Blazor'</span></span>
- <span data-ttu-id="cefdc-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-384">'Identity'</span></span>
- <span data-ttu-id="cefdc-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-386">'Razor'</span></span>
- <span data-ttu-id="cefdc-387">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-388">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-388">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-389">'Blazor'</span></span>
- <span data-ttu-id="cefdc-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-390">'Identity'</span></span>
- <span data-ttu-id="cefdc-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-392">'Razor'</span></span>
- <span data-ttu-id="cefdc-393">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-394">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-394">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-395">'Blazor'</span></span>
- <span data-ttu-id="cefdc-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-396">'Identity'</span></span>
- <span data-ttu-id="cefdc-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-398">'Razor'</span></span>
- <span data-ttu-id="cefdc-399">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-400">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-400">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-401">'Blazor'</span></span>
- <span data-ttu-id="cefdc-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-402">'Identity'</span></span>
- <span data-ttu-id="cefdc-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-404">'Razor'</span></span>
- <span data-ttu-id="cefdc-405">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-407">'Blazor'</span></span>
- <span data-ttu-id="cefdc-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-408">'Identity'</span></span>
- <span data-ttu-id="cefdc-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-410">'Razor'</span></span>
- <span data-ttu-id="cefdc-411">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-413">'Blazor'</span></span>
- <span data-ttu-id="cefdc-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-414">'Identity'</span></span>
- <span data-ttu-id="cefdc-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-416">'Razor'</span></span>
- <span data-ttu-id="cefdc-417">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-418">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-418">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-419">'Blazor'</span></span>
- <span data-ttu-id="cefdc-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-420">'Identity'</span></span>
- <span data-ttu-id="cefdc-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-422">'Razor'</span></span>
- <span data-ttu-id="cefdc-423">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-423">'SignalR' uid:</span></span> 

<span data-ttu-id="cefdc-424">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-424">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-425">'Blazor'</span></span>
- <span data-ttu-id="cefdc-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-426">'Identity'</span></span>
- <span data-ttu-id="cefdc-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-428">'Razor'</span></span>
- <span data-ttu-id="cefdc-429">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-431">'Blazor'</span></span>
- <span data-ttu-id="cefdc-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-432">'Identity'</span></span>
- <span data-ttu-id="cefdc-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-434">'Razor'</span></span>
- <span data-ttu-id="cefdc-435">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-436">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-436">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-437">'Blazor'</span></span>
- <span data-ttu-id="cefdc-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-438">'Identity'</span></span>
- <span data-ttu-id="cefdc-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-440">'Razor'</span></span>
- <span data-ttu-id="cefdc-441">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cefdc-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cefdc-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cefdc-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-443">'Blazor'</span></span>
- <span data-ttu-id="cefdc-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cefdc-444">'Identity'</span></span>
- <span data-ttu-id="cefdc-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cefdc-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="cefdc-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cefdc-446">'Razor'</span></span>
- <span data-ttu-id="cefdc-447">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cefdc-447">'SignalR' uid:</span></span> 

<span data-ttu-id="cefdc-448">------ | | `http://mysite.azurewebsites.net/`     | Web アプリ      | | `http://mysite.scm.azurewebsites.net/` | Kudu サービス |</span><span class="sxs-lookup"><span data-stu-id="cefdc-448">------ | | `http://mysite.azurewebsites.net/`     | Web App      | | `http://mysite.scm.azurewebsites.net/` | Kudu service |</span></span>

<span data-ttu-id="cefdc-449">ファイルの表示、編集、削除、追加を行うには、[[デバッグ コンソール]](https://github.com/projectkudu/kudu/wiki/Kudu-console) メニュー項目を選択します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-449">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cefdc-450">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cefdc-450">Additional resources</span></span>

* <span data-ttu-id="cefdc-451">[Web 配置](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) は、IIS サーバーへの Web アプリと Web サイトの配置を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="cefdc-451">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="cefdc-452">[Web SDK GitHub リポジトリ](https://github.com/aspnet/websdk/issues):配置でのファイルの問題と要求機能。</span><span class="sxs-lookup"><span data-stu-id="cefdc-452">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="cefdc-453">Visual Studio から Azure VM に ASP.NET Web アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="cefdc-453">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
