---
<span data-ttu-id="1a2c0-101">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-103">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-104">'Identity'</span></span>
- <span data-ttu-id="1a2c0-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-106">'Razor'</span></span>
- <span data-ttu-id="1a2c0-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="1a2c0-108">ASP.NET Core Blazor 用のリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="1a2c0-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="1a2c0-109">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1a2c0-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="1a2c0-110"> WebAssembly では、ビルド中に[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) のリンクが実行されて、アプリの出力アセンブリから不要な IL がトリミングされます。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="1a2c0-111">デバッグ構成でビルドすると、リンカーは無効になります。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="1a2c0-112">リンカーを有効にするには、アプリをリリース構成でビルドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="1a2c0-113">Blazor WebAssembly アプリを配置する場合は、リリースでビルドすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="1a2c0-114">アプリをリンクするとサイズが最適化されますが、悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="1a2c0-115">リフレクションや関連する動的機能を使用するアプリは、トリミングされたときに中断する可能性があります。リンカーがこの動的な動作を認識せず、通常は実行時にリフレクションに必要な型を特定できないためです。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="1a2c0-116">そのようなアプリをトリミングするには、コードと、アプリが依存しているパッケージまたはフレームワークのリフレクションで必要なすべての型を、リンカーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="1a2c0-117">トリミングされたアプリが配置後に正しく動作するには、開発中にアプリのリリース ビルドを頻繁にテストすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="1a2c0-118">Blazor アプリのリンクは、次の MSBuild 機能を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="1a2c0-119">[MSBuild プロパティ](#control-linking-with-an-msbuild-property)を使ってリンクをグローバルに構成する。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="1a2c0-120">[構成ファイル](#control-linking-with-a-configuration-file)を使ってアセンブリごとにリンクを制御する。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="1a2c0-121">MSBuild プロパティを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="1a2c0-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="1a2c0-122">リンクは、アプリが `Release` 構成でビルドされると有効になります。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="1a2c0-123">これを変更するには、プロジェクト ファイルで `BlazorWebAssemblyEnableLinking` の MSBuild プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="1a2c0-124">構成ファイルを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="1a2c0-124">Control linking with a configuration file</span></span>

<span data-ttu-id="1a2c0-125">XML の構成ファイルを用意してそのファイルをプロジェクト ファイル内で MSBuild 項目として指定することで、アセンブリごとにリンクを制御します。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="1a2c0-126">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-126">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="1a2c0-127">詳細と例については、[データ形式 (mono/linker GitHub リポジトリ)](https://github.com/mono/linker/blob/master/docs/data-formats.md) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="1a2c0-128">XML リンカー構成ファイルをライブラリに追加する</span><span class="sxs-lookup"><span data-stu-id="1a2c0-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="1a2c0-129">特定のライブラリ用にリンカーを構成するには、XML リンカー構成ファイルを埋め込みリソースとしてライブラリに追加します。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="1a2c0-130">埋め込みリソースの名前は、アセンブリと同じにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="1a2c0-131">次の例では、*LinkerConfig.xml* ファイルが、ライブラリのアセンブリと同じ名前を持つ埋め込みリソースとして指定されています。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="1a2c0-132">国際化用にリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="1a2c0-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="1a2c0-133">既定では、Blazor WebAssembly に対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="1a2c0-134">これらのアセンブリを削除すると、アプリのサイズが最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="1a2c0-135">保持される I18N アセンブリを制御するには、プロジェクト ファイルで MSBuild のプロパティ `<BlazorWebAssemblyI18NAssemblies>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="1a2c0-136">リージョンの値</span><span class="sxs-lookup"><span data-stu-id="1a2c0-136">Region Value</span></span>     | <span data-ttu-id="1a2c0-137">Mono のリージョン アセンブリ</span><span class="sxs-lookup"><span data-stu-id="1a2c0-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="1a2c0-138">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-140">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-141">'Identity'</span></span>
- <span data-ttu-id="1a2c0-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-143">'Razor'</span></span>
- <span data-ttu-id="1a2c0-144">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-145">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-147">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-148">'Identity'</span></span>
- <span data-ttu-id="1a2c0-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-150">'Razor'</span></span>
- <span data-ttu-id="1a2c0-151">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-152">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-154">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-155">'Identity'</span></span>
- <span data-ttu-id="1a2c0-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-157">'Razor'</span></span>
- <span data-ttu-id="1a2c0-158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-159">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-161">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-162">'Identity'</span></span>
- <span data-ttu-id="1a2c0-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-164">'Razor'</span></span>
- <span data-ttu-id="1a2c0-165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-166">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-168">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-169">'Identity'</span></span>
- <span data-ttu-id="1a2c0-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-171">'Razor'</span></span>
- <span data-ttu-id="1a2c0-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-173">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-175">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-176">'Identity'</span></span>
- <span data-ttu-id="1a2c0-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-178">'Razor'</span></span>
- <span data-ttu-id="1a2c0-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-179">'SignalR' uid:</span></span> 

<span data-ttu-id="1a2c0-180">-------- | --- title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-182">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-183">'Identity'</span></span>
- <span data-ttu-id="1a2c0-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-185">'Razor'</span></span>
- <span data-ttu-id="1a2c0-186">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-187">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-189">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-190">'Identity'</span></span>
- <span data-ttu-id="1a2c0-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-192">'Razor'</span></span>
- <span data-ttu-id="1a2c0-193">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-194">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-196">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-197">'Identity'</span></span>
- <span data-ttu-id="1a2c0-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-199">'Razor'</span></span>
- <span data-ttu-id="1a2c0-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-201">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-203">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-204">'Identity'</span></span>
- <span data-ttu-id="1a2c0-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-206">'Razor'</span></span>
- <span data-ttu-id="1a2c0-207">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-208">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-210">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-211">'Identity'</span></span>
- <span data-ttu-id="1a2c0-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-213">'Razor'</span></span>
- <span data-ttu-id="1a2c0-214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-215">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-217">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-218">'Identity'</span></span>
- <span data-ttu-id="1a2c0-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-220">'Razor'</span></span>
- <span data-ttu-id="1a2c0-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-222">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-224">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-225">'Identity'</span></span>
- <span data-ttu-id="1a2c0-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-227">'Razor'</span></span>
- <span data-ttu-id="1a2c0-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-229">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-231">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-232">'Identity'</span></span>
- <span data-ttu-id="1a2c0-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-234">'Razor'</span></span>
- <span data-ttu-id="1a2c0-235">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1a2c0-236">title:'ASP.NET Core のリンカーを構成する Blazor 用のリンカーを構成する' author: description:Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="1a2c0-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a2c0-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-238">'Blazor'</span></span>
- <span data-ttu-id="1a2c0-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-239">'Identity'</span></span>
- <span data-ttu-id="1a2c0-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a2c0-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a2c0-241">'Razor'</span></span>
- <span data-ttu-id="1a2c0-242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1a2c0-242">'SignalR' uid:</span></span> 

<span data-ttu-id="1a2c0-243">------------ | | `all`            | すべてのアセンブリが含まれます | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (既定) | なし                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span><span class="sxs-lookup"><span data-stu-id="1a2c0-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="1a2c0-244">複数の値を区切るにはコンマを使用します (例: `mideast,west`)。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="1a2c0-245">詳しくは、「[I18N: Pnetlib 国際化フレームワーク ライブラリ (mono/mono GitHub リポジトリ)](https://github.com/mono/mono/tree/master/mcs/class/I18N)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1a2c0-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a2c0-246">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1a2c0-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
