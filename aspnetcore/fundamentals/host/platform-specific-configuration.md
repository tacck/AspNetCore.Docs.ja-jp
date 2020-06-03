---
<span data-ttu-id="5f078-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5f078-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5f078-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5f078-102">'Blazor'</span></span>
- <span data-ttu-id="5f078-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5f078-103">'Identity'</span></span>
- <span data-ttu-id="5f078-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5f078-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5f078-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5f078-105">'Razor'</span></span>
- <span data-ttu-id="5f078-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5f078-106">'SignalR' uid:</span></span> 

---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="5f078-107">ASP.NET Core でホスティング スタートアップ アセンブリを使用する</span><span class="sxs-lookup"><span data-stu-id="5f078-107">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="5f078-108">作成者: [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="5f078-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5f078-109"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (ホスティング スタートアップ) の実装を使うと、外部アセンブリからの起動時にアプリに拡張機能を追加できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="5f078-110">たとえば、外部ライブラリではホスティング スタートアップ実装を使用して、追加の構成プロバイダーまたはサービスをアプリに提供できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="5f078-111">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5f078-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="5f078-112">HostingStartup 属性</span><span class="sxs-lookup"><span data-stu-id="5f078-112">HostingStartup attribute</span></span>

<span data-ttu-id="5f078-113">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性は、実行時にアクティブ化されるホスティング スタートアップ アセンブリが存在することを示しています。</span><span class="sxs-lookup"><span data-stu-id="5f078-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="5f078-114">入力アセンブリまたは `Startup` クラスを含むアセンブリは、`HostingStartup` 属性について自動的にスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="5f078-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5f078-115">`HostingStartup` 属性を検索するアセンブリの一覧は、[WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 内の構成からランタイム時に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="5f078-116">検出から除外されるアセンブリの一覧は、[WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="5f078-117">次に例では、ホスティング スタートアップ アセンブリの名前空間は `StartupEnhancement` となります。</span><span class="sxs-lookup"><span data-stu-id="5f078-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="5f078-118">ホスティング スタートアップ コードを含むクラスは `StartupEnhancementHostingStartup` です。</span><span class="sxs-lookup"><span data-stu-id="5f078-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5f078-119">`HostingStartup` 属性は、通常、ホスティング スタートアップ アセンブリの `IHostingStartup` 実装クラス ファイル内に置かれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="5f078-120">読み込まれたホスティング スタートアップ アセンブリを見つける</span><span class="sxs-lookup"><span data-stu-id="5f078-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="5f078-121">読み込まれたホスティング スタートアップ アセンブリを検出するには、ログ記録を有効にしてアプリのログを確認します。</span><span class="sxs-lookup"><span data-stu-id="5f078-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="5f078-122">アセンブリの読み込み時に発生したエラーがログ記録されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-122">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="5f078-123">読み込まれたホスティング スタートアップ アセンブリは、デバッグ レベルでログ記録され、すべてのエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="5f078-124">ホスティング スタートアップ アセンブリの自動読み込みを無効にする</span><span class="sxs-lookup"><span data-stu-id="5f078-124">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="5f078-125">ホスティング スタートアップ アセンブリの自動読み込みを無効にするには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f078-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="5f078-126">すべてのホスティング スタートアップ アセンブリの読み込みを回避するには、次のいずれかを `true` または `1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="5f078-127">[ホスティング スタートアップを回避する] ホスト構成設定。</span><span class="sxs-lookup"><span data-stu-id="5f078-127">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="5f078-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="5f078-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="5f078-129">特定のホスティング スタートアップ アセンブリの読み込みを回避するには、次に示すいずれかを、起動時に除外するホスティング スタートアップ アセンブリを示すセミコロンで区切られた文字列に設定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="5f078-130">[Hosting Startup Exclude Assemblies]\(除外するホスティング スタートアップ アセンブリ\) ホスト構成設定。</span><span class="sxs-lookup"><span data-stu-id="5f078-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="5f078-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="5f078-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="5f078-132">ホスト構成設定と環境変数が両方とも設定されている場合は、ホスト設定によって動作が制御されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="5f078-133">ホスト設定または環境変数を使用してホスティング スタートアップ アセンブリを無効にすると、アセンブリがグローバルに無効になり、場合によってはアプリのいくつかの属性も無効になります。</span><span class="sxs-lookup"><span data-stu-id="5f078-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="5f078-134">Project</span><span class="sxs-lookup"><span data-stu-id="5f078-134">Project</span></span>

<span data-ttu-id="5f078-135">次のいずれかの種類のプロジェクトを使用して、ホスティング スタートアップを作成します。</span><span class="sxs-lookup"><span data-stu-id="5f078-135">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="5f078-136">クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="5f078-136">Class library</span></span>](#class-library)
* [<span data-ttu-id="5f078-137">エントリ ポイントのないコンソール アプリ</span><span class="sxs-lookup"><span data-stu-id="5f078-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="5f078-138">クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="5f078-138">Class library</span></span>

<span data-ttu-id="5f078-139">クラス ライブラリでは、ホスティング スタートアップの拡張機能を提供できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="5f078-140">このライブラリには `HostingStartup` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-140">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5f078-141">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)には、Razor Pages アプリ、*HostingStartupApp*、クラス ライブラリ、*HostingStartupLibrary* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="5f078-142">クラス ライブラリには次のものが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-142">The class library:</span></span>

* <span data-ttu-id="5f078-143">`IHostingStartup` を実装するホスティング スタートアップ クラス `ServiceKeyInjection`。</span><span class="sxs-lookup"><span data-stu-id="5f078-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5f078-144">`ServiceKeyInjection` では、メモリ内の構成プロバイダー ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) を使用して、サービスの文字列のペアがアプリの構成に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="5f078-145">ホスティング スタートアップの名前空間とクラスを識別する `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="5f078-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="5f078-146">`ServiceKeyInjection` クラスの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドでは、<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> を使ってアプリに拡張機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="5f078-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f078-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5f078-148">アプリのインデックス ページでは、クラス ライブラリのホスティング スタートアップ アセンブリによって設定された 2 つのキーの構成値が読み取られて表示されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="5f078-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f078-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="5f078-150">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) には、別のホスティング スタートアップ *HostingStartupPackage* を提供する NuGet パッケージ プロジェクトも含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="5f078-151">パッケージには、前に説明したクラス ライブラリと同じ特性があります。</span><span class="sxs-lookup"><span data-stu-id="5f078-151">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="5f078-152">パッケージには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-152">The package:</span></span>

* <span data-ttu-id="5f078-153">`IHostingStartup` を実装するホスティング スタートアップ クラス `ServiceKeyInjection`。</span><span class="sxs-lookup"><span data-stu-id="5f078-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5f078-154">`ServiceKeyInjection` では、アプリの構成にサービス文字列のペアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="5f078-155">`HostingStartup`属性。</span><span class="sxs-lookup"><span data-stu-id="5f078-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5f078-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f078-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5f078-157">アプリのインデックス ページでは、パッケージのホスティング スタートアップ アセンブリによって設定された 2 つのキーの構成値が読み取られて表示されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="5f078-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f078-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="5f078-159">エントリ ポイントのないコンソール アプリ</span><span class="sxs-lookup"><span data-stu-id="5f078-159">Console app without an entry point</span></span>

<span data-ttu-id="5f078-160">*このアプローチは、.NET Core アプリでのみ利用でき、.NET Framework では利用できません。*</span><span class="sxs-lookup"><span data-stu-id="5f078-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="5f078-161">`HostingStartup` 属性を含むエントリ ポイントがないコンソール アプリ内では、アクティブ化に関するコンパイル時参照を必要としない動的ホスティング スタートアップ拡張機能を指定できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="5f078-162">コンソール アプリを発行すると、ランタイム ストアから使用できるホスティング スタートアップ アセンブリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="5f078-163">このプロセスにおいてエントリ ポイントのないコンソール アプリが使用される理由は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5f078-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="5f078-164">ホスティング スタートアップ アセンブリ内のホスティング スタートアップを使用するには、依存関係ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="5f078-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="5f078-165">依存関係ファイルは、ライブラリではなくアプリを発行することによって生成される、実行可能なアプリ資産です。</span><span class="sxs-lookup"><span data-stu-id="5f078-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="5f078-166">[ランタイム パッケージ ストア](/dotnet/core/deploying/runtime-store) にライブラリを直接追加することはできません。それには、共有ランタイムを対象とする実行可能なプロジェクトが必要です。</span><span class="sxs-lookup"><span data-stu-id="5f078-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="5f078-167">動的ホスティング スタートアップを作成する場合:</span><span class="sxs-lookup"><span data-stu-id="5f078-167">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="5f078-168">次のようなエントリ ポイントがないコンソール アプリからホスティング スタートアップ アセンブリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="5f078-169">`IHostingStartup` の実装を含むクラスを含んでいる。</span><span class="sxs-lookup"><span data-stu-id="5f078-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="5f078-170">`IHostingStartup` 実装クラスを識別するための [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性を含んでいる。</span><span class="sxs-lookup"><span data-stu-id="5f078-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="5f078-171">ホスティング スタートアップの依存関係を取得するには、コンソール アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-171">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="5f078-172">コンソール アプリを発行すると、その結果として、依存関係ファイルから未使用の依存関係が切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="5f078-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="5f078-173">ホスティング スタートアップ アセンブリのランタイムの場所を設定するために、依存関係ファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="5f078-174">ホスティング スタートアップ アセンブリとその依存関係ファイルは、ランタイム パッケージ ストアに配置されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="5f078-175">ホスティング スタートアップ アセンブリとその依存関係ファイルを検出する場合、それらは環境変数のペアに記載されています。</span><span class="sxs-lookup"><span data-stu-id="5f078-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="5f078-176">コンソール アセンブリでは、[Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) パッケージが参照されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="5f078-177">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性を使うと、<xref:Microsoft.AspNetCore.Hosting.IWebHost> のビルド時に、読み込みと実行のための `IHostingStartup` の実装としてクラスが識別されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="5f078-178">次の例では、名前空間は `StartupEnhancement`、クラスは `StartupEnhancementHostingStartup` です。</span><span class="sxs-lookup"><span data-stu-id="5f078-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5f078-179">クラスは `IHostingStartup` を実装しています。</span><span class="sxs-lookup"><span data-stu-id="5f078-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="5f078-180">このクラスの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドでは、<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> を使ってアプリに拡張機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="5f078-181">ホスティング スタートアップ アセンブリ内の `IHostingStartup.Configure` は、ユーザー コード内の `Startup.Configure` よりも前にランタイムによって呼び出されます。このため、ホスティング スタートアップ アセンブリによって提供されるすべての構成をユーザー コードで上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="5f078-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="5f078-182">`IHostingStartup` プロジェクトのビルド時に、依存関係ファイル ( *.deps.json*) によってアセンブリの `runtime` の場所が *bin* フォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="5f078-183">ファイルの一部のみが示されています。</span><span class="sxs-lookup"><span data-stu-id="5f078-183">Only part of the file is shown.</span></span> <span data-ttu-id="5f078-184">例のアセンブリ名は `StartupEnhancement` です。</span><span class="sxs-lookup"><span data-stu-id="5f078-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="5f078-185">ホスティング スタートアップによって指定される構成</span><span class="sxs-lookup"><span data-stu-id="5f078-185">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="5f078-186">ホスティング スタートアップの構成、アプリの構成のどちらを優先させるかによって、2 つの異なる方法で構成を処理できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="5f078-187">アプリの <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> デリゲートを実行した後で <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> を使って構成を読み込み、アプリに構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5f078-188">この方法を使うと、ホスティング スタートアップの構成の方がアプリの構成よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="5f078-189">アプリの <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> デリゲートを実行する前に <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を使って構成を読み込み、アプリに構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5f078-190">この方法を使うと、アプリの構成値の方がホスティング スタートアップにより指定されるものよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="5f078-191">ホスティング スタートアップ アセンブリを指定する</span><span class="sxs-lookup"><span data-stu-id="5f078-191">Specify the hosting startup assembly</span></span>

<span data-ttu-id="5f078-192">クラス ライブラリまたはコンソール アプリのいずれかで提供されるホスティング スタートアップの場合は、`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数内にホスティング スタートアップ アセンブリの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="5f078-193">環境変数はアセンブリのセミコロン区切りのリストです。</span><span class="sxs-lookup"><span data-stu-id="5f078-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="5f078-194">ホスティング スタートアップ アセンブリのみが、`HostingStartup` 属性に対してスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="5f078-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5f078-195">サンプル アプリ *HostingStartupApp* では、前述したホスティング スタートアップを検出するために、環境変数が次の値に設定されています。</span><span class="sxs-lookup"><span data-stu-id="5f078-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="5f078-196">また、ホスティング スタートアップ アセンブリは、[ホスティング スタートアップ アセンブリ] のホスト構成設定を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="5f078-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="5f078-197">複数のホスティング スタートアップ アセンブリが存在する場合、それらの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドは、アセンブリが一覧表示されている順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="5f078-198">アクティベーション</span><span class="sxs-lookup"><span data-stu-id="5f078-198">Activation</span></span>

<span data-ttu-id="5f078-199">ホスティング スタートアップのアクティブ化のオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5f078-199">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="5f078-200">[ランタイム ストア](#runtime-store):アクティブ化では、アクティブ化に関するコンパイル時参照を必要としません。</span><span class="sxs-lookup"><span data-stu-id="5f078-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="5f078-201">サンプル アプリでは、ホスティング スタートアップ アセンブリおよび依存関係のファイルが *deployment* フォルダーに配置されています。これにより、複数のコンピューターから成る環境でのホスティング スタートアップの配置が容易になります。</span><span class="sxs-lookup"><span data-stu-id="5f078-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="5f078-202">*deployment* フォルダーには、ホスティングスタートアップが有効になるように配置システム上で環境変数を作成および変更する PowerShell スクリプトも含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="5f078-203">アクティブ化で必須のコンパイル時参照</span><span class="sxs-lookup"><span data-stu-id="5f078-203">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="5f078-204">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="5f078-204">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="5f078-205">プロジェクトの bin フォルダー</span><span class="sxs-lookup"><span data-stu-id="5f078-205">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="5f078-206">ランタイム ストア</span><span class="sxs-lookup"><span data-stu-id="5f078-206">Runtime store</span></span>

<span data-ttu-id="5f078-207">ホスティング スタートアップ実装は、[ランタイム ストア](/dotnet/core/deploying/runtime-store)に置かれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="5f078-208">アセンブリへのコンパイル時参照は、機能強化されたアプリで必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="5f078-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="5f078-209">ホスティング スタートアップをビルドした後、プロジェクトのマニフェスト ファイルと [dotnet store](/dotnet/core/tools/dotnet-store) コマンドの使用によってランタイム ストアが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="5f078-210">サンプル アプリ (*RuntimeStore* プロジェクト) では、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f078-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="5f078-211">ランタイムでランタイム ストアを検出できるように、ランタイム ストアの場所を環境変数 `DOTNET_SHARED_STORE` に追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="5f078-212">**ホスティング スタートアップの依存関係ファイルを変更して配置する**</span><span class="sxs-lookup"><span data-stu-id="5f078-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="5f078-213">拡張機能へのパッケージ参照なしで拡張機能をアクティブ化するには、`additionalDeps` を使用してランタイムに追加の依存関係を指定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="5f078-214">`additionalDeps` を使用すると、次のことを実行できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-214">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="5f078-215">スタートアップ時にアプリの独自の *.deps.json* ファイルとマージさせる追加の *.deps.json* ファイルのセットを指定することで、アプリのライブラリのグラフを拡張します。</span><span class="sxs-lookup"><span data-stu-id="5f078-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="5f078-216">ホスティング スタートアップ アセンブリを検出可能および読み込み可能にします。</span><span class="sxs-lookup"><span data-stu-id="5f078-216">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="5f078-217">追加の依存関係ファイルを生成するために推奨される方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5f078-217">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="5f078-218">前のセクションで参照したランタイム ストアのマニフェスト ファイルに対して `dotnet publish` を実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="5f078-219">ライブラリと、結果として得られる *deps.json* ファイルの `runtime` セクションから、マニフェスト参照を削除します。</span><span class="sxs-lookup"><span data-stu-id="5f078-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="5f078-220">プロジェクトの例では、`targets` と `libraries` セクションから `store.manifest/1.0.0` プロパティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="5f078-221">次の場所に *.deps.json* ファイルを配置します。</span><span class="sxs-lookup"><span data-stu-id="5f078-221">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="5f078-222">`{ADDITIONAL DEPENDENCIES PATH}`:`DOTNET_ADDITIONAL_DEPS` 環境変数に追加される場所。</span><span class="sxs-lookup"><span data-stu-id="5f078-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="5f078-223">`{SHARED FRAMEWORK NAME}`:この追加の依存関係ファイルに必要な共有フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="5f078-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="5f078-224">`{SHARED FRAMEWORK VERSION}`:共有フレームワークの最小バージョン。</span><span class="sxs-lookup"><span data-stu-id="5f078-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="5f078-225">`{ENHANCEMENT ASSEMBLY NAME}`:拡張機能のアセンブリ名。</span><span class="sxs-lookup"><span data-stu-id="5f078-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="5f078-226">サンプル アプリ (*RuntimeStore* プロジェクト) では、追加の依存関係ファイルは以下の場所に配置されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="5f078-227">ランタイムでランタイム ストアの場所を検出できるように、追加の依存関係ファイルの場所が環境変数 `DOTNET_ADDITIONAL_DEPS` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="5f078-228">サンプル アプリ (*RuntimeStore* プロジェクト) では、[PowerShell](/powershell/scripting/powershell-scripting) スクリプトを使用してランタイム ストアのビルドと追加の依存関係ファイルの生成を行います。</span><span class="sxs-lookup"><span data-stu-id="5f078-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="5f078-229">さまざまなオペレーティング システムの環境変数を設定する方法の例については、[複数の環境の使用](xref:fundamentals/environments)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5f078-230">**配置**</span><span class="sxs-lookup"><span data-stu-id="5f078-230">**Deployment**</span></span>

<span data-ttu-id="5f078-231">サンプル アプリでは、複数のコンピューターから成る環境へのホスティング スタートアップの配置を容易にするために、発行された出力内に、次を含む *deployment* フォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="5f078-232">ホスティング スタートアップのランタイム ストア。</span><span class="sxs-lookup"><span data-stu-id="5f078-232">The hosting startup runtime store.</span></span>
* <span data-ttu-id="5f078-233">ホスティング スタートアップの依存関係ファイル。</span><span class="sxs-lookup"><span data-stu-id="5f078-233">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="5f078-234">ホスティング スタートアップのアクティブ化をサポートできるように、`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE`、`DOTNET_ADDITIONAL_DEPS` を作成および変更する PowerShell スクリプト。</span><span class="sxs-lookup"><span data-stu-id="5f078-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="5f078-235">このスクリプトは、配置システム上の PowerShell 管理用コマンド プロンプトから実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="5f078-236">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="5f078-236">NuGet package</span></span>

<span data-ttu-id="5f078-237">NuGet パッケージでは、ホスティング スタートアップの拡張機能を提供できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="5f078-238">このパッケージには `HostingStartup` 属性があります。</span><span class="sxs-lookup"><span data-stu-id="5f078-238">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="5f078-239">このパッケージで提供される種類のホスティング スタートアップは、次のアプローチのいずれかを使用してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f078-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="5f078-240">機能強化されたアプリのプロジェクト ファイルでは、アプリのプロジェクト ファイル内のホスティング スタートアップに対するパッケージ参照 (コンパイル時参照) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="5f078-241">コンパイル時参照を適用すると、ホスティング スタートアップ アセンブリとその依存関係のすべてが、アプリの依存関係ファイル ( *.deps.json*) に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5f078-242">このアプローチは、[nuget.org](https://www.nuget.org/) に発行されるホスティング スタートアップ アセンブリ パッケージに適用されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="5f078-243">ホスティング スタートアップの依存関係ファイルは、「[ランタイム ストア](#runtime-store)」のセクションで説明にしたように (コンパイル参照がない場合)、機能強化されたアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f078-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="5f078-244">NuGet パッケージとランタイム ストアの詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="5f078-245">クロスプラットフォーム ツールを使用して NuGet パッケージを作成する方法</span><span class="sxs-lookup"><span data-stu-id="5f078-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="5f078-246">パッケージを公開する</span><span class="sxs-lookup"><span data-stu-id="5f078-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="5f078-247">ランタイム パッケージ ストア</span><span class="sxs-lookup"><span data-stu-id="5f078-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="5f078-248">プロジェクトの bin フォルダー</span><span class="sxs-lookup"><span data-stu-id="5f078-248">Project bin folder</span></span>

<span data-ttu-id="5f078-249">ホスティング スタートアップの拡張機能は、機能強化されたアプリ内の *bin* 配置アセンブリによって提供できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="5f078-250">このアセンブリで提供される種類のホスティング スタートアップは、次のアプローチのいずれかを使用してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f078-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="5f078-251">機能強化されたアプリのプロジェクト ファイルでは、ホスティング スタートアップへのアセンブリ参照 (コンパイル時参照) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="5f078-252">コンパイル時参照を適用すると、ホスティング スタートアップ アセンブリとその依存関係のすべてが、アプリの依存関係ファイル ( *.deps.json*) に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5f078-253">この手法は、展開シナリオによって、ホスティング スタートアップのアセンブリ ( *.dll* ファイル) に対してコンパイル時参照を作成し、次のいずれかにそのアセンブリを移動させることが要請される場合に適用されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="5f078-254">使用するプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="5f078-254">The consuming project.</span></span>
  * <span data-ttu-id="5f078-255">使用するプロジェクトでアクセスできる場所。</span><span class="sxs-lookup"><span data-stu-id="5f078-255">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="5f078-256">ホスティング スタートアップの依存関係ファイルは、「[ランタイム ストア](#runtime-store)」のセクションで説明にしたように (コンパイル参照がない場合)、機能強化されたアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f078-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="5f078-257">.NET Framework を対象にするとき、アセンブリは既定の読み込みコンテキストで読み込み可能です。これは、.NET Framework の場合、アセンブリが次のいずれかの場所にあることを意味します。</span><span class="sxs-lookup"><span data-stu-id="5f078-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="5f078-258">アプリケーション ベース パス:アプリの実行可能ファイル ( *.exe*) が置かれている *bin* フォルダー。</span><span class="sxs-lookup"><span data-stu-id="5f078-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="5f078-259">グローバル アセンブリ キャッシュ (GAC):GAC には、複数の .NET Framework アプリで共有されるアセンブリが格納されています。</span><span class="sxs-lookup"><span data-stu-id="5f078-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="5f078-260">詳細については、[ アセンブリをグローバル アセンブリ キャッシュにインストールする](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)」を参照してください。これは .NET Framework ドキュメントにあります。</span><span class="sxs-lookup"><span data-stu-id="5f078-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="5f078-261">サンプル コード</span><span class="sxs-lookup"><span data-stu-id="5f078-261">Sample code</span></span>

<span data-ttu-id="5f078-262">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([ダウンロードする方法](xref:index#how-to-download-a-sample)) では、ホスティング スタートアップ実装のシナリオを示します。</span><span class="sxs-lookup"><span data-stu-id="5f078-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="5f078-263">2 つのホスティング スタートアップ アセンブリ (クラス ライブラリ) ではそれぞれ、メモリ内の構成のキーと値のペアの組が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="5f078-264">NuGet パッケージ (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="5f078-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="5f078-265">クラス ライブラリ (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="5f078-265">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="5f078-266">ホスティング スタートアップは、ランタイム ストア配置アセンブリ (*StartupDiagnostics*) からアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="5f078-267">このアセンブリによってスタートアップ時に 2 つのミドルウェアがアプリに追加され、これにより診断情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="5f078-268">登録サービス</span><span class="sxs-lookup"><span data-stu-id="5f078-268">Registered services</span></span>
  * <span data-ttu-id="5f078-269">アドレス (スキーム、ホスト、パス ベース、パス、クエリ文字列)</span><span class="sxs-lookup"><span data-stu-id="5f078-269">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="5f078-270">接続 (リモート IP、リモート ポート、ローカル IP、ローカル ポート、クライアント証明書)</span><span class="sxs-lookup"><span data-stu-id="5f078-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="5f078-271">要求ヘッダー</span><span class="sxs-lookup"><span data-stu-id="5f078-271">Request headers</span></span>
  * <span data-ttu-id="5f078-272">環境変数</span><span class="sxs-lookup"><span data-stu-id="5f078-272">Environment variables</span></span>

<span data-ttu-id="5f078-273">サンプルを実行するには</span><span class="sxs-lookup"><span data-stu-id="5f078-273">To run the sample:</span></span>

<span data-ttu-id="5f078-274">**NuGet パッケージからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="5f078-274">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="5f078-275">[dotnet pack](/dotnet/core/tools/dotnet-pack) コマンドを使用して *HostingStartupPackage* パッケージをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="5f078-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="5f078-276">*HostingStartupPackage* のパッケージのアセンブリ名を `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5f078-277">アプリをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-277">Compile and run the app.</span></span> <span data-ttu-id="5f078-278">機能拡張されたアプリにはパッケージ参照 (コンパイル時参照) が存在します。</span><span class="sxs-lookup"><span data-stu-id="5f078-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="5f078-279">アプリのプロジェクト ファイル内の `<PropertyGroup>` では、パッケージ プロジェクトの出力 ( *../HostingStartupPackage/Bin/debug*) がパッケージ ソースとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="5f078-280">これにより、[nuget.org](https://www.nuget.org/) にパッケージをアップロードすることなく、アプリによりパッケージが使用されるようになります。詳細については、HostingStartupApp のプロジェクト ファイル内の注を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5f078-281">インデックス ページで表示されるサービス構成キー値が、パッケージの `ServiceKeyInjection.Configure` メソッドによって設定された値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="5f078-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5f078-282">*HostingStartupPackage* プロジェクトに変更を加えてそれをコンパイルする場合は、ローカル キャッシュから、古いパッケージではなく更新されたパッケージが、*HostingStartupApp* によって確実に受信されるように、ローカルの NuGet パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="5f078-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="5f078-283">ローカルの NuGet キャッシュをクリアするには、次の [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="5f078-284">**クラス ライブラリからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="5f078-284">**Activation from a class library**</span></span>

1. <span data-ttu-id="5f078-285">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドを使用して *HostingStartupLibrary* クラス ライブラリをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="5f078-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="5f078-286">*HostingStartupLibrary* のクラス ライブラリのアセンブリ名を `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5f078-287">クラス ライブラリのアセンブリをアプリに *bin* 配置するには、クラス ライブラリのコンパイルされた出力からアプリの *bin/Debug* フォルダーに *HostingStartupLibrary.dll* ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="5f078-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="5f078-288">アプリをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-288">Compile and run the app.</span></span> <span data-ttu-id="5f078-289">アプリのプロジェクト ファイル内の `<ItemGroup>` により、クラス ライブラリのアセンブリ ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) が参照されます (コンパイル時参照)。</span><span class="sxs-lookup"><span data-stu-id="5f078-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="5f078-290">詳細については、HostingStartupApp のプロジェクト ファイル内の注を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-290">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="5f078-291">インデックス ページで表示されるサービス構成キー値が、クラス ライブラリの `ServiceKeyInjection.Configure` メソッドによって設定された値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="5f078-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5f078-292">**ランタイム ストア配置アセンブリからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="5f078-292">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="5f078-293">*StartupDiagnostics* プロジェクトでは、[PowerShell](/powershell/scripting/powershell-scripting) を使用して、その *StartupDiagnostics.deps.json* ファイルを変更します。</span><span class="sxs-lookup"><span data-stu-id="5f078-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="5f078-294">既定では、PowerShell は Windows 7 SP1 と Windows Server 2008 R2 SP1 以降の Windows 上にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="5f078-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="5f078-295">他のプラットフォームで PowerShell を取得するには、「[PowerShell のさまざまなバージョンのインストール](/powershell/scripting/install/installing-powershell)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="5f078-296">*RuntimeStore* フォルダーにある *build.ps1* スクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="5f078-297">スクリプトでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="5f078-297">The script:</span></span>
   * <span data-ttu-id="5f078-298">*obj\packages* フォルダーに `StartupDiagnostics` パッケージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="5f078-299">*store* フォルダー内に `StartupDiagnostics` 用のランタイム ストアが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="5f078-300">スクリプト内の `dotnet store` コマンドでは、Windows に展開されているホスティング スタートアップの `win7-x64`[ランタイム識別子 (RID)](/dotnet/core/rid-catalog) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="5f078-301">別のランタイムに対してホスティング スタートアップを指定する場合は、スクリプトの 37 行目を適切な RID に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="5f078-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="5f078-302">`StartupDiagnostics` のランタイム ストアは、後で、アセンブリが使用されるコンピューター上のユーザーまたはシステムのランタイム ストアに移動されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="5f078-303">`StartupDiagnostics` アセンブリのユーザー ランタイム ストアのインストール場所は、 *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll* です。</span><span class="sxs-lookup"><span data-stu-id="5f078-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="5f078-304">*additionalDeps* フォルダーに `StartupDiagnostics` の `additionalDeps` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="5f078-305">追加の依存関係は、後でユーザーまたはシステムの追加の依存関係に移動されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="5f078-306">ユーザーの `StartupDiagnostics` に関する追加の依存関係のインストール場所は、 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json* です。</span><span class="sxs-lookup"><span data-stu-id="5f078-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="5f078-307">*deploy.ps1* ファイルが *deployment* フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="5f078-308">*deployment* フォルダーにある *deploy.ps1* スクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="5f078-309">スクリプトでは以下のものが追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-309">The script appends:</span></span>
   * <span data-ttu-id="5f078-310">`StartupDiagnostics` が `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="5f078-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="5f078-311">(RuntimeStore プロジェクトの *deployment*  フォルダーにある) ホスティング スタートアップの依存関係のパスが、`DOTNET_ADDITIONAL_DEPS` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="5f078-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="5f078-312">(RuntimeStore プロジェクトの *deployment*  フォルダーにある) ランタイム ストアのパスが、`DOTNET_SHARED_STORE` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="5f078-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="5f078-313">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-313">Run the sample app.</span></span>
1. <span data-ttu-id="5f078-314">`/services` エンドポイントを要求して、アプリの登録サービスを参照します。</span><span class="sxs-lookup"><span data-stu-id="5f078-314">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="5f078-315">`/diag` エンドポイントを要求して、診断情報を参照します。</span><span class="sxs-lookup"><span data-stu-id="5f078-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5f078-316"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (ホスティング スタートアップ) の実装を使うと、外部アセンブリからの起動時にアプリに拡張機能を追加できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="5f078-317">たとえば、外部ライブラリではホスティング スタートアップ実装を使用して、追加の構成プロバイダーまたはサービスをアプリに提供できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="5f078-318">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5f078-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="5f078-319">HostingStartup 属性</span><span class="sxs-lookup"><span data-stu-id="5f078-319">HostingStartup attribute</span></span>

<span data-ttu-id="5f078-320">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性は、実行時にアクティブ化されるホスティング スタートアップ アセンブリが存在することを示しています。</span><span class="sxs-lookup"><span data-stu-id="5f078-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="5f078-321">入力アセンブリまたは `Startup` クラスを含むアセンブリは、`HostingStartup` 属性について自動的にスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="5f078-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5f078-322">`HostingStartup` 属性を検索するアセンブリの一覧は、[WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 内の構成からランタイム時に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="5f078-323">検出から除外されるアセンブリの一覧は、[WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="5f078-324">詳細については、「[Web ホスト: ホスティング スタートアップ アセンブリ](xref:fundamentals/host/web-host#hosting-startup-assemblies)と[Web ホスト: 除外するホスティング スタートアップ アセンブリ](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)</span><span class="sxs-lookup"><span data-stu-id="5f078-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="5f078-325">次に例では、ホスティング スタートアップ アセンブリの名前空間は `StartupEnhancement` となります。</span><span class="sxs-lookup"><span data-stu-id="5f078-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="5f078-326">ホスティング スタートアップ コードを含むクラスは `StartupEnhancementHostingStartup` です。</span><span class="sxs-lookup"><span data-stu-id="5f078-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5f078-327">`HostingStartup` 属性は、通常、ホスティング スタートアップ アセンブリの `IHostingStartup` 実装クラス ファイル内に置かれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="5f078-328">読み込まれたホスティング スタートアップ アセンブリを見つける</span><span class="sxs-lookup"><span data-stu-id="5f078-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="5f078-329">読み込まれたホスティング スタートアップ アセンブリを検出するには、ログ記録を有効にしてアプリのログを確認します。</span><span class="sxs-lookup"><span data-stu-id="5f078-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="5f078-330">アセンブリの読み込み時に発生したエラーがログ記録されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-330">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="5f078-331">読み込まれたホスティング スタートアップ アセンブリは、デバッグ レベルでログ記録され、すべてのエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="5f078-332">ホスティング スタートアップ アセンブリの自動読み込みを無効にする</span><span class="sxs-lookup"><span data-stu-id="5f078-332">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="5f078-333">ホスティング スタートアップ アセンブリの自動読み込みを無効にするには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f078-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="5f078-334">すべてのホスティング スタートアップ アセンブリの読み込みを回避するには、次のいずれかを `true` または `1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="5f078-335">[[ホスティング スタートアップを回避する]](xref:fundamentals/host/web-host#prevent-hosting-startup) ホスト構成設定。</span><span class="sxs-lookup"><span data-stu-id="5f078-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="5f078-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="5f078-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="5f078-337">特定のホスティング スタートアップ アセンブリの読み込みを回避するには、次に示すいずれかを、起動時に除外するホスティング スタートアップ アセンブリを示すセミコロンで区切られた文字列に設定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="5f078-338">[[Hosting Startup Exclude Assemblies]\(除外するホスティング スタートアップ アセンブリ\)](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ホスト構成設定。</span><span class="sxs-lookup"><span data-stu-id="5f078-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="5f078-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="5f078-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="5f078-340">ホスト構成設定と環境変数が両方とも設定されている場合は、ホスト設定によって動作が制御されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="5f078-341">ホスト設定または環境変数を使用してホスティング スタートアップ アセンブリを無効にすると、アセンブリがグローバルに無効になり、場合によってはアプリのいくつかの属性も無効になります。</span><span class="sxs-lookup"><span data-stu-id="5f078-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="5f078-342">Project</span><span class="sxs-lookup"><span data-stu-id="5f078-342">Project</span></span>

<span data-ttu-id="5f078-343">次のいずれかの種類のプロジェクトを使用して、ホスティング スタートアップを作成します。</span><span class="sxs-lookup"><span data-stu-id="5f078-343">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="5f078-344">クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="5f078-344">Class library</span></span>](#class-library)
* [<span data-ttu-id="5f078-345">エントリ ポイントのないコンソール アプリ</span><span class="sxs-lookup"><span data-stu-id="5f078-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="5f078-346">クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="5f078-346">Class library</span></span>

<span data-ttu-id="5f078-347">クラス ライブラリでは、ホスティング スタートアップの拡張機能を提供できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="5f078-348">このライブラリには `HostingStartup` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-348">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5f078-349">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)には、Razor Pages アプリ、*HostingStartupApp*、クラス ライブラリ、*HostingStartupLibrary* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="5f078-350">クラス ライブラリには次のものが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-350">The class library:</span></span>

* <span data-ttu-id="5f078-351">`IHostingStartup` を実装するホスティング スタートアップ クラス `ServiceKeyInjection`。</span><span class="sxs-lookup"><span data-stu-id="5f078-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5f078-352">`ServiceKeyInjection` では、メモリ内の構成プロバイダー ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) を使用して、サービスの文字列のペアがアプリの構成に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="5f078-353">ホスティング スタートアップの名前空間とクラスを識別する `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="5f078-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="5f078-354">`ServiceKeyInjection` クラスの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドでは、<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> を使ってアプリに拡張機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="5f078-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f078-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5f078-356">アプリのインデックス ページでは、クラス ライブラリのホスティング スタートアップ アセンブリによって設定された 2 つのキーの構成値が読み取られて表示されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="5f078-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f078-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="5f078-358">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) には、別のホスティング スタートアップ *HostingStartupPackage* を提供する NuGet パッケージ プロジェクトも含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="5f078-359">パッケージには、前に説明したクラス ライブラリと同じ特性があります。</span><span class="sxs-lookup"><span data-stu-id="5f078-359">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="5f078-360">パッケージには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-360">The package:</span></span>

* <span data-ttu-id="5f078-361">`IHostingStartup` を実装するホスティング スタートアップ クラス `ServiceKeyInjection`。</span><span class="sxs-lookup"><span data-stu-id="5f078-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5f078-362">`ServiceKeyInjection` では、アプリの構成にサービス文字列のペアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="5f078-363">`HostingStartup`属性。</span><span class="sxs-lookup"><span data-stu-id="5f078-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5f078-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f078-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5f078-365">アプリのインデックス ページでは、パッケージのホスティング スタートアップ アセンブリによって設定された 2 つのキーの構成値が読み取られて表示されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="5f078-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f078-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="5f078-367">エントリ ポイントのないコンソール アプリ</span><span class="sxs-lookup"><span data-stu-id="5f078-367">Console app without an entry point</span></span>

<span data-ttu-id="5f078-368">*このアプローチは、.NET Core アプリでのみ利用でき、.NET Framework では利用できません。*</span><span class="sxs-lookup"><span data-stu-id="5f078-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="5f078-369">`HostingStartup` 属性を含むエントリ ポイントがないコンソール アプリ内では、アクティブ化に関するコンパイル時参照を必要としない動的ホスティング スタートアップ拡張機能を指定できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="5f078-370">コンソール アプリを発行すると、ランタイム ストアから使用できるホスティング スタートアップ アセンブリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="5f078-371">このプロセスにおいてエントリ ポイントのないコンソール アプリが使用される理由は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5f078-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="5f078-372">ホスティング スタートアップ アセンブリ内のホスティング スタートアップを使用するには、依存関係ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="5f078-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="5f078-373">依存関係ファイルは、ライブラリではなくアプリを発行することによって生成される、実行可能なアプリ資産です。</span><span class="sxs-lookup"><span data-stu-id="5f078-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="5f078-374">[ランタイム パッケージ ストア](/dotnet/core/deploying/runtime-store) にライブラリを直接追加することはできません。それには、共有ランタイムを対象とする実行可能なプロジェクトが必要です。</span><span class="sxs-lookup"><span data-stu-id="5f078-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="5f078-375">動的ホスティング スタートアップを作成する場合:</span><span class="sxs-lookup"><span data-stu-id="5f078-375">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="5f078-376">次のようなエントリ ポイントがないコンソール アプリからホスティング スタートアップ アセンブリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="5f078-377">`IHostingStartup` の実装を含むクラスを含んでいる。</span><span class="sxs-lookup"><span data-stu-id="5f078-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="5f078-378">`IHostingStartup` 実装クラスを識別するための [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性を含んでいる。</span><span class="sxs-lookup"><span data-stu-id="5f078-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="5f078-379">ホスティング スタートアップの依存関係を取得するには、コンソール アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-379">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="5f078-380">コンソール アプリを発行すると、その結果として、依存関係ファイルから未使用の依存関係が切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="5f078-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="5f078-381">ホスティング スタートアップ アセンブリのランタイムの場所を設定するために、依存関係ファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="5f078-382">ホスティング スタートアップ アセンブリとその依存関係ファイルは、ランタイム パッケージ ストアに配置されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="5f078-383">ホスティング スタートアップ アセンブリとその依存関係ファイルを検出する場合、それらは環境変数のペアに記載されています。</span><span class="sxs-lookup"><span data-stu-id="5f078-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="5f078-384">コンソール アセンブリでは、[Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) パッケージが参照されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="5f078-385">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性を使うと、<xref:Microsoft.AspNetCore.Hosting.IWebHost> のビルド時に、読み込みと実行のための `IHostingStartup` の実装としてクラスが識別されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="5f078-386">次の例では、名前空間は `StartupEnhancement`、クラスは `StartupEnhancementHostingStartup` です。</span><span class="sxs-lookup"><span data-stu-id="5f078-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5f078-387">クラスは `IHostingStartup` を実装しています。</span><span class="sxs-lookup"><span data-stu-id="5f078-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="5f078-388">このクラスの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドでは、<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> を使ってアプリに拡張機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="5f078-389">ホスティング スタートアップ アセンブリ内の `IHostingStartup.Configure` は、ユーザー コード内の `Startup.Configure` よりも前にランタイムによって呼び出されます。このため、ホスティング スタートアップ アセンブリによって提供されるすべての構成をユーザー コードで上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="5f078-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="5f078-390">`IHostingStartup` プロジェクトのビルド時に、依存関係ファイル ( *.deps.json*) によってアセンブリの `runtime` の場所が *bin* フォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="5f078-391">ファイルの一部のみが示されています。</span><span class="sxs-lookup"><span data-stu-id="5f078-391">Only part of the file is shown.</span></span> <span data-ttu-id="5f078-392">例のアセンブリ名は `StartupEnhancement` です。</span><span class="sxs-lookup"><span data-stu-id="5f078-392">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="5f078-393">ホスティング スタートアップによって指定される構成</span><span class="sxs-lookup"><span data-stu-id="5f078-393">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="5f078-394">ホスティング スタートアップの構成、アプリの構成のどちらを優先させるかによって、2 つの異なる方法で構成を処理できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="5f078-395">アプリの <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> デリゲートを実行した後で <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> を使って構成を読み込み、アプリに構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5f078-396">この方法を使うと、ホスティング スタートアップの構成の方がアプリの構成よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="5f078-397">アプリの <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> デリゲートを実行する前に <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を使って構成を読み込み、アプリに構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5f078-398">この方法を使うと、アプリの構成値の方がホスティング スタートアップにより指定されるものよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="5f078-399">ホスティング スタートアップ アセンブリを指定する</span><span class="sxs-lookup"><span data-stu-id="5f078-399">Specify the hosting startup assembly</span></span>

<span data-ttu-id="5f078-400">クラス ライブラリまたはコンソール アプリのいずれかで提供されるホスティング スタートアップの場合は、`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数内にホスティング スタートアップ アセンブリの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="5f078-401">環境変数はアセンブリのセミコロン区切りのリストです。</span><span class="sxs-lookup"><span data-stu-id="5f078-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="5f078-402">ホスティング スタートアップ アセンブリのみが、`HostingStartup` 属性に対してスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="5f078-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5f078-403">サンプル アプリ *HostingStartupApp* では、前述したホスティング スタートアップを検出するために、環境変数が次の値に設定されています。</span><span class="sxs-lookup"><span data-stu-id="5f078-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="5f078-404">ホスティング スタートアップ アセンブリはまた、[[ホスティング スタートアップ アセンブリ]](xref:fundamentals/host/web-host#hosting-startup-assemblies) ホスト構成設定を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="5f078-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="5f078-405">複数のホスティング スタートアップ アセンブリが存在する場合、それらの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドは、アセンブリが一覧表示されている順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="5f078-406">アクティベーション</span><span class="sxs-lookup"><span data-stu-id="5f078-406">Activation</span></span>

<span data-ttu-id="5f078-407">ホスティング スタートアップのアクティブ化のオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5f078-407">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="5f078-408">[ランタイム ストア](#runtime-store):アクティブ化では、アクティブ化に関するコンパイル時参照を必要としません。</span><span class="sxs-lookup"><span data-stu-id="5f078-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="5f078-409">サンプル アプリでは、ホスティング スタートアップ アセンブリおよび依存関係のファイルが *deployment* フォルダーに配置されています。これにより、複数のコンピューターから成る環境でのホスティング スタートアップの配置が容易になります。</span><span class="sxs-lookup"><span data-stu-id="5f078-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="5f078-410">*deployment* フォルダーには、ホスティングスタートアップが有効になるように配置システム上で環境変数を作成および変更する PowerShell スクリプトも含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f078-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="5f078-411">アクティブ化で必須のコンパイル時参照</span><span class="sxs-lookup"><span data-stu-id="5f078-411">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="5f078-412">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="5f078-412">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="5f078-413">プロジェクトの bin フォルダー</span><span class="sxs-lookup"><span data-stu-id="5f078-413">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="5f078-414">ランタイム ストア</span><span class="sxs-lookup"><span data-stu-id="5f078-414">Runtime store</span></span>

<span data-ttu-id="5f078-415">ホスティング スタートアップ実装は、[ランタイム ストア](/dotnet/core/deploying/runtime-store)に置かれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="5f078-416">アセンブリへのコンパイル時参照は、機能強化されたアプリで必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="5f078-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="5f078-417">ホスティング スタートアップをビルドした後、プロジェクトのマニフェスト ファイルと [dotnet store](/dotnet/core/tools/dotnet-store) コマンドの使用によってランタイム ストアが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="5f078-418">サンプル アプリ (*RuntimeStore* プロジェクト) では、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f078-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="5f078-419">ランタイムでランタイム ストアを検出できるように、ランタイム ストアの場所を環境変数 `DOTNET_SHARED_STORE` に追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="5f078-420">**ホスティング スタートアップの依存関係ファイルを変更して配置する**</span><span class="sxs-lookup"><span data-stu-id="5f078-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="5f078-421">拡張機能へのパッケージ参照なしで拡張機能をアクティブ化するには、`additionalDeps` を使用してランタイムに追加の依存関係を指定します。</span><span class="sxs-lookup"><span data-stu-id="5f078-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="5f078-422">`additionalDeps` を使用すると、次のことを実行できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-422">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="5f078-423">スタートアップ時にアプリの独自の *.deps.json* ファイルとマージさせる追加の *.deps.json* ファイルのセットを指定することで、アプリのライブラリのグラフを拡張します。</span><span class="sxs-lookup"><span data-stu-id="5f078-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="5f078-424">ホスティング スタートアップ アセンブリを検出可能および読み込み可能にします。</span><span class="sxs-lookup"><span data-stu-id="5f078-424">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="5f078-425">追加の依存関係ファイルを生成するために推奨される方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5f078-425">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="5f078-426">前のセクションで参照したランタイム ストアのマニフェスト ファイルに対して `dotnet publish` を実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="5f078-427">ライブラリと、結果として得られる *deps.json* ファイルの `runtime` セクションから、マニフェスト参照を削除します。</span><span class="sxs-lookup"><span data-stu-id="5f078-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="5f078-428">プロジェクトの例では、`targets` と `libraries` セクションから `store.manifest/1.0.0` プロパティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="5f078-429">次の場所に *.deps.json* ファイルを配置します。</span><span class="sxs-lookup"><span data-stu-id="5f078-429">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="5f078-430">`{ADDITIONAL DEPENDENCIES PATH}`:`DOTNET_ADDITIONAL_DEPS` 環境変数に追加される場所。</span><span class="sxs-lookup"><span data-stu-id="5f078-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="5f078-431">`{SHARED FRAMEWORK NAME}`:この追加の依存関係ファイルに必要な共有フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="5f078-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="5f078-432">`{SHARED FRAMEWORK VERSION}`:共有フレームワークの最小バージョン。</span><span class="sxs-lookup"><span data-stu-id="5f078-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="5f078-433">`{ENHANCEMENT ASSEMBLY NAME}`:拡張機能のアセンブリ名。</span><span class="sxs-lookup"><span data-stu-id="5f078-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="5f078-434">サンプル アプリ (*RuntimeStore* プロジェクト) では、追加の依存関係ファイルは以下の場所に配置されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="5f078-435">ランタイムでランタイム ストアの場所を検出できるように、追加の依存関係ファイルの場所が環境変数 `DOTNET_ADDITIONAL_DEPS` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="5f078-436">サンプル アプリ (*RuntimeStore* プロジェクト) では、[PowerShell](/powershell/scripting/powershell-scripting) スクリプトを使用してランタイム ストアのビルドと追加の依存関係ファイルの生成を行います。</span><span class="sxs-lookup"><span data-stu-id="5f078-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="5f078-437">さまざまなオペレーティング システムの環境変数を設定する方法の例については、[複数の環境の使用](xref:fundamentals/environments)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5f078-438">**配置**</span><span class="sxs-lookup"><span data-stu-id="5f078-438">**Deployment**</span></span>

<span data-ttu-id="5f078-439">サンプル アプリでは、複数のコンピューターから成る環境へのホスティング スタートアップの配置を容易にするために、発行された出力内に、次を含む *deployment* フォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="5f078-440">ホスティング スタートアップのランタイム ストア。</span><span class="sxs-lookup"><span data-stu-id="5f078-440">The hosting startup runtime store.</span></span>
* <span data-ttu-id="5f078-441">ホスティング スタートアップの依存関係ファイル。</span><span class="sxs-lookup"><span data-stu-id="5f078-441">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="5f078-442">ホスティング スタートアップのアクティブ化をサポートできるように、`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE`、`DOTNET_ADDITIONAL_DEPS` を作成および変更する PowerShell スクリプト。</span><span class="sxs-lookup"><span data-stu-id="5f078-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="5f078-443">このスクリプトは、配置システム上の PowerShell 管理用コマンド プロンプトから実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="5f078-444">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="5f078-444">NuGet package</span></span>

<span data-ttu-id="5f078-445">NuGet パッケージでは、ホスティング スタートアップの拡張機能を提供できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="5f078-446">このパッケージには `HostingStartup` 属性があります。</span><span class="sxs-lookup"><span data-stu-id="5f078-446">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="5f078-447">このパッケージで提供される種類のホスティング スタートアップは、次のアプローチのいずれかを使用してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f078-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="5f078-448">機能強化されたアプリのプロジェクト ファイルでは、アプリのプロジェクト ファイル内のホスティング スタートアップに対するパッケージ参照 (コンパイル時参照) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="5f078-449">コンパイル時参照を適用すると、ホスティング スタートアップ アセンブリとその依存関係のすべてが、アプリの依存関係ファイル ( *.deps.json*) に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5f078-450">このアプローチは、[nuget.org](https://www.nuget.org/) に発行されるホスティング スタートアップ アセンブリ パッケージに適用されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="5f078-451">ホスティング スタートアップの依存関係ファイルは、「[ランタイム ストア](#runtime-store)」のセクションで説明にしたように (コンパイル参照がない場合)、機能強化されたアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f078-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="5f078-452">NuGet パッケージとランタイム ストアの詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="5f078-453">クロスプラットフォーム ツールを使用して NuGet パッケージを作成する方法</span><span class="sxs-lookup"><span data-stu-id="5f078-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="5f078-454">パッケージを公開する</span><span class="sxs-lookup"><span data-stu-id="5f078-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="5f078-455">ランタイム パッケージ ストア</span><span class="sxs-lookup"><span data-stu-id="5f078-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="5f078-456">プロジェクトの bin フォルダー</span><span class="sxs-lookup"><span data-stu-id="5f078-456">Project bin folder</span></span>

<span data-ttu-id="5f078-457">ホスティング スタートアップの拡張機能は、機能強化されたアプリ内の *bin* 配置アセンブリによって提供できます。</span><span class="sxs-lookup"><span data-stu-id="5f078-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="5f078-458">このアセンブリで提供される種類のホスティング スタートアップは、次のアプローチのいずれかを使用してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f078-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="5f078-459">機能強化されたアプリのプロジェクト ファイルでは、ホスティング スタートアップへのアセンブリ参照 (コンパイル時参照) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="5f078-460">コンパイル時参照を適用すると、ホスティング スタートアップ アセンブリとその依存関係のすべてが、アプリの依存関係ファイル ( *.deps.json*) に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5f078-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5f078-461">この手法は、展開シナリオによって、ホスティング スタートアップのアセンブリ ( *.dll* ファイル) に対してコンパイル時参照を作成し、次のいずれかにそのアセンブリを移動させることが要請される場合に適用されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="5f078-462">使用するプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="5f078-462">The consuming project.</span></span>
  * <span data-ttu-id="5f078-463">使用するプロジェクトでアクセスできる場所。</span><span class="sxs-lookup"><span data-stu-id="5f078-463">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="5f078-464">ホスティング スタートアップの依存関係ファイルは、「[ランタイム ストア](#runtime-store)」のセクションで説明にしたように (コンパイル参照がない場合)、機能強化されたアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f078-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="5f078-465">.NET Framework を対象にするとき、アセンブリは既定の読み込みコンテキストで読み込み可能です。これは、.NET Framework の場合、アセンブリが次のいずれかの場所にあることを意味します。</span><span class="sxs-lookup"><span data-stu-id="5f078-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="5f078-466">アプリケーション ベース パス:アプリの実行可能ファイル ( *.exe*) が置かれている *bin* フォルダー。</span><span class="sxs-lookup"><span data-stu-id="5f078-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="5f078-467">グローバル アセンブリ キャッシュ (GAC):GAC には、複数の .NET Framework アプリで共有されるアセンブリが格納されています。</span><span class="sxs-lookup"><span data-stu-id="5f078-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="5f078-468">詳細については、[ アセンブリをグローバル アセンブリ キャッシュにインストールする](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)」を参照してください。これは .NET Framework ドキュメントにあります。</span><span class="sxs-lookup"><span data-stu-id="5f078-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="5f078-469">サンプル コード</span><span class="sxs-lookup"><span data-stu-id="5f078-469">Sample code</span></span>

<span data-ttu-id="5f078-470">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([ダウンロードする方法](xref:index#how-to-download-a-sample)) では、ホスティング スタートアップ実装のシナリオを示します。</span><span class="sxs-lookup"><span data-stu-id="5f078-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="5f078-471">2 つのホスティング スタートアップ アセンブリ (クラス ライブラリ) ではそれぞれ、メモリ内の構成のキーと値のペアの組が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="5f078-472">NuGet パッケージ (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="5f078-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="5f078-473">クラス ライブラリ (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="5f078-473">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="5f078-474">ホスティング スタートアップは、ランタイム ストア配置アセンブリ (*StartupDiagnostics*) からアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="5f078-475">このアセンブリによってスタートアップ時に 2 つのミドルウェアがアプリに追加され、これにより診断情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="5f078-476">登録サービス</span><span class="sxs-lookup"><span data-stu-id="5f078-476">Registered services</span></span>
  * <span data-ttu-id="5f078-477">アドレス (スキーム、ホスト、パス ベース、パス、クエリ文字列)</span><span class="sxs-lookup"><span data-stu-id="5f078-477">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="5f078-478">接続 (リモート IP、リモート ポート、ローカル IP、ローカル ポート、クライアント証明書)</span><span class="sxs-lookup"><span data-stu-id="5f078-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="5f078-479">要求ヘッダー</span><span class="sxs-lookup"><span data-stu-id="5f078-479">Request headers</span></span>
  * <span data-ttu-id="5f078-480">環境変数</span><span class="sxs-lookup"><span data-stu-id="5f078-480">Environment variables</span></span>

<span data-ttu-id="5f078-481">サンプルを実行するには</span><span class="sxs-lookup"><span data-stu-id="5f078-481">To run the sample:</span></span>

<span data-ttu-id="5f078-482">**NuGet パッケージからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="5f078-482">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="5f078-483">[dotnet pack](/dotnet/core/tools/dotnet-pack) コマンドを使用して *HostingStartupPackage* パッケージをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="5f078-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="5f078-484">*HostingStartupPackage* のパッケージのアセンブリ名を `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5f078-485">アプリをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-485">Compile and run the app.</span></span> <span data-ttu-id="5f078-486">機能拡張されたアプリにはパッケージ参照 (コンパイル時参照) が存在します。</span><span class="sxs-lookup"><span data-stu-id="5f078-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="5f078-487">アプリのプロジェクト ファイル内の `<PropertyGroup>` では、パッケージ プロジェクトの出力 ( *../HostingStartupPackage/Bin/debug*) がパッケージ ソースとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="5f078-488">これにより、[nuget.org](https://www.nuget.org/) にパッケージをアップロードすることなく、アプリによりパッケージが使用されるようになります。詳細については、HostingStartupApp のプロジェクト ファイル内の注を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5f078-489">インデックス ページで表示されるサービス構成キー値が、パッケージの `ServiceKeyInjection.Configure` メソッドによって設定された値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="5f078-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5f078-490">*HostingStartupPackage* プロジェクトに変更を加えてそれをコンパイルする場合は、ローカル キャッシュから、古いパッケージではなく更新されたパッケージが、*HostingStartupApp* によって確実に受信されるように、ローカルの NuGet パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="5f078-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="5f078-491">ローカルの NuGet キャッシュをクリアするには、次の [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="5f078-492">**クラス ライブラリからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="5f078-492">**Activation from a class library**</span></span>

1. <span data-ttu-id="5f078-493">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドを使用して *HostingStartupLibrary* クラス ライブラリをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="5f078-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="5f078-494">*HostingStartupLibrary* のクラス ライブラリのアセンブリ名を `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="5f078-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5f078-495">クラス ライブラリのアセンブリをアプリに *bin* 配置するには、クラス ライブラリのコンパイルされた出力からアプリの *bin/Debug* フォルダーに *HostingStartupLibrary.dll* ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="5f078-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="5f078-496">アプリをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-496">Compile and run the app.</span></span> <span data-ttu-id="5f078-497">アプリのプロジェクト ファイル内の `<ItemGroup>` は、クラス ライブラリのアセンブリ ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) を参照します (コンパイル時参照)。</span><span class="sxs-lookup"><span data-stu-id="5f078-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="5f078-498">詳細については、HostingStartupApp のプロジェクト ファイル内の注を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-498">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="5f078-499">インデックス ページで表示されるサービス構成キー値が、クラス ライブラリの `ServiceKeyInjection.Configure` メソッドによって設定された値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="5f078-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5f078-500">**ランタイム ストア配置アセンブリからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="5f078-500">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="5f078-501">*StartupDiagnostics* プロジェクトでは、[PowerShell](/powershell/scripting/powershell-scripting) を使用して、その *StartupDiagnostics.deps.json* ファイルを変更します。</span><span class="sxs-lookup"><span data-stu-id="5f078-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="5f078-502">既定では、PowerShell は Windows 7 SP1 と Windows Server 2008 R2 SP1 以降の Windows 上にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="5f078-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="5f078-503">他のプラットフォームで PowerShell を取得するには、「[PowerShell のさまざまなバージョンのインストール](/powershell/scripting/install/installing-powershell)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f078-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="5f078-504">*RuntimeStore* フォルダーにある *build.ps1* スクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="5f078-505">スクリプトでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="5f078-505">The script:</span></span>
   * <span data-ttu-id="5f078-506">*obj\packages* フォルダーに `StartupDiagnostics` パッケージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="5f078-507">*store* フォルダー内に `StartupDiagnostics` 用のランタイム ストアが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="5f078-508">スクリプト内の `dotnet store` コマンドでは、Windows に展開されているホスティング スタートアップの `win7-x64`[ランタイム識別子 (RID)](/dotnet/core/rid-catalog) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="5f078-509">別のランタイムに対してホスティング スタートアップを指定する場合は、スクリプトの 37 行目を適切な RID に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="5f078-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="5f078-510">`StartupDiagnostics` のランタイム ストアは、後で、アセンブリが使用されるコンピューター上のユーザーまたはシステムのランタイム ストアに移動されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="5f078-511">`StartupDiagnostics` アセンブリのユーザー ランタイム ストアのインストール場所は、 *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll* です。</span><span class="sxs-lookup"><span data-stu-id="5f078-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="5f078-512">*additionalDeps* フォルダーに `StartupDiagnostics` の `additionalDeps` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="5f078-513">追加の依存関係は、後でユーザーまたはシステムの追加の依存関係に移動されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="5f078-514">ユーザーの `StartupDiagnostics` に関する追加の依存関係のインストール場所は、 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json* です。</span><span class="sxs-lookup"><span data-stu-id="5f078-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="5f078-515">*deploy.ps1* ファイルが *deployment* フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="5f078-516">*deployment* フォルダーにある *deploy.ps1* スクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="5f078-517">スクリプトでは以下のものが追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f078-517">The script appends:</span></span>
   * <span data-ttu-id="5f078-518">`StartupDiagnostics` が `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="5f078-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="5f078-519">(RuntimeStore プロジェクトの *deployment*  フォルダーにある) ホスティング スタートアップの依存関係のパスが、`DOTNET_ADDITIONAL_DEPS` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="5f078-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="5f078-520">(RuntimeStore プロジェクトの *deployment*  フォルダーにある) ランタイム ストアのパスが、`DOTNET_SHARED_STORE` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="5f078-520">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="5f078-521">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f078-521">Run the sample app.</span></span>
1. <span data-ttu-id="5f078-522">`/services` エンドポイントを要求して、アプリの登録サービスを参照します。</span><span class="sxs-lookup"><span data-stu-id="5f078-522">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="5f078-523">`/diag` エンドポイントを要求して、診断情報を参照します。</span><span class="sxs-lookup"><span data-stu-id="5f078-523">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
