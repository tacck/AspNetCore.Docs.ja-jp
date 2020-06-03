---
<span data-ttu-id="79539-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-102">'Blazor'</span></span>
- <span data-ttu-id="79539-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-103">'Identity'</span></span>
- <span data-ttu-id="79539-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-105">'Razor'</span></span>
- <span data-ttu-id="79539-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="79539-107">ASP.NET Core でのファイル プロバイダー</span><span class="sxs-lookup"><span data-stu-id="79539-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="79539-108">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="79539-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79539-109">ASP.NET Core は、ファイル プロバイダーを使用してファイル システムへのアクセスを抽象化します。</span><span class="sxs-lookup"><span data-stu-id="79539-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="79539-110">ファイル プロバイダーは、ASP.NET Core フレームワーク全体で使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="79539-111">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="79539-111">For example:</span></span>

* <span data-ttu-id="79539-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> では、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)と [Web ルート](xref:fundamentals/index#web-root)が `IFileProvider` 型として公開されます。</span><span class="sxs-lookup"><span data-stu-id="79539-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="79539-113">[静的ファイル ミドルウェア](xref:fundamentals/static-files)では、ファイル プロバイダーを使用して静的なファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="79539-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="79539-114">[Razor](xref:mvc/views/razor) では、ファイル プロバイダーを使用してページとビューを見つけます。</span><span class="sxs-lookup"><span data-stu-id="79539-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="79539-115">.NET Core Tooling では、ファイル プロバイダーと glob パターンを使用して、公開するファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="79539-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="79539-116">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="79539-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="79539-117">ファイル プロバイダーのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="79539-117">File Provider interfaces</span></span>

<span data-ttu-id="79539-118">プライマリ インターフェイスは <xref:Microsoft.Extensions.FileProviders.IFileProvider> です。</span><span class="sxs-lookup"><span data-stu-id="79539-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="79539-119">`IFileProvider` では次のためのメソッドが公開されます。</span><span class="sxs-lookup"><span data-stu-id="79539-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="79539-120">ファイルの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="79539-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="79539-121">ディレクトリの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="79539-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="79539-122">変更通知を設定します (<xref:Microsoft.Extensions.Primitives.IChangeToken> を使用)。</span><span class="sxs-lookup"><span data-stu-id="79539-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="79539-123">`IFileInfo` ではファイルを操作するためのメソッドとプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="79539-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="79539-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (バイト単位)</span><span class="sxs-lookup"><span data-stu-id="79539-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="79539-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> の日付</span><span class="sxs-lookup"><span data-stu-id="79539-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="79539-126"><xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> メソッドを使用して、ファイルから情報を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="79539-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="79539-127">*FileProviderSample* サンプル アプリでは、[依存関係の挿入](xref:fundamentals/dependency-injection)を介してアプリ全体で使用するために、`Startup.ConfigureServices` でファイル プロバイダーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="79539-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="79539-128">ファイル プロバイダーの実装</span><span class="sxs-lookup"><span data-stu-id="79539-128">File Provider implementations</span></span>

<span data-ttu-id="79539-129">次の表に、`IFileProvider` の実装の一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="79539-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="79539-130">実装</span><span class="sxs-lookup"><span data-stu-id="79539-130">Implementation</span></span> | <span data-ttu-id="79539-131">説明</span><span class="sxs-lookup"><span data-stu-id="79539-131">Description</span></span> |
| ---
<span data-ttu-id="79539-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-133">'Blazor'</span></span>
- <span data-ttu-id="79539-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-134">'Identity'</span></span>
- <span data-ttu-id="79539-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-136">'Razor'</span></span>
- <span data-ttu-id="79539-137">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-139">'Blazor'</span></span>
- <span data-ttu-id="79539-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-140">'Identity'</span></span>
- <span data-ttu-id="79539-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-142">'Razor'</span></span>
- <span data-ttu-id="79539-143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-145">'Blazor'</span></span>
- <span data-ttu-id="79539-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-146">'Identity'</span></span>
- <span data-ttu-id="79539-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-148">'Razor'</span></span>
- <span data-ttu-id="79539-149">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-151">'Blazor'</span></span>
- <span data-ttu-id="79539-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-152">'Identity'</span></span>
- <span data-ttu-id="79539-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-154">'Razor'</span></span>
- <span data-ttu-id="79539-155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-157">'Blazor'</span></span>
- <span data-ttu-id="79539-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-158">'Identity'</span></span>
- <span data-ttu-id="79539-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-160">'Razor'</span></span>
- <span data-ttu-id="79539-161">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-161">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-163">'Blazor'</span></span>
- <span data-ttu-id="79539-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-164">'Identity'</span></span>
- <span data-ttu-id="79539-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-166">'Razor'</span></span>
- <span data-ttu-id="79539-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-169">'Blazor'</span></span>
- <span data-ttu-id="79539-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-170">'Identity'</span></span>
- <span data-ttu-id="79539-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-172">'Razor'</span></span>
- <span data-ttu-id="79539-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-175">'Blazor'</span></span>
- <span data-ttu-id="79539-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-176">'Identity'</span></span>
- <span data-ttu-id="79539-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-178">'Razor'</span></span>
- <span data-ttu-id="79539-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-179">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-180">------ | | [CompositeFileProvider](#compositefileprovider) | その他の 1 つまたは複数のプロバイダーからのファイルおよびディレクトリに対するアクセスを結合する場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="79539-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | アセンブリ内に埋め込まれているファイルにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="79539-182">| | [PhysicalFileProvider](#physicalfileprovider) | システムの物理ファイルにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="79539-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="79539-183">PhysicalFileProvider</span></span>

<span data-ttu-id="79539-184"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> は、物理ファイル システムへのアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="79539-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="79539-185">`PhysicalFileProvider` では、<xref:System.IO.File?displayProperty=fullName> 型が使用され (物理プロバイダーの場合)、すべてのパスのスコープが 1 つのディレクトリとその子ディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="79539-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="79539-186">このスコープ設定により、指定されたディレクトリとその子ディレクトリを除くファイル システムにアクセスできなくなります。</span><span class="sxs-lookup"><span data-stu-id="79539-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="79539-187">`PhysicalFileProvider` を作成して使用する最も一般的なシナリオは、[依存関係の挿入](xref:fundamentals/dependency-injection)を通してコンストラクターで `IFileProvider` を要求する場合です。</span><span class="sxs-lookup"><span data-stu-id="79539-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="79539-188">このプロバイダーを直接インスタンス化するときは、絶対ディレクトリ パスが要求され、そのプロバイダーを使用して行われるすべての要求のベース パスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="79539-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="79539-189">glob パターンはディレクトリ パスではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="79539-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="79539-190">次のコードは、`PhysicalFileProvider` を使用してディレクトリの内容とファイルの情報を取得する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="79539-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="79539-191">前の例の型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="79539-191">Types in the preceding example:</span></span>

* <span data-ttu-id="79539-192">`provider` は `IFileProvider` です。</span><span class="sxs-lookup"><span data-stu-id="79539-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="79539-193">`contents` は `IDirectoryContents` です。</span><span class="sxs-lookup"><span data-stu-id="79539-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="79539-194">`fileInfo` は `IFileInfo` です。</span><span class="sxs-lookup"><span data-stu-id="79539-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="79539-195">ファイル プロバイダーを使用して、`applicationRoot` で指定したディレクトリ全体を反復処理したり、`GetFileInfo` を呼び出してファイル情報を取得したりできます。</span><span class="sxs-lookup"><span data-stu-id="79539-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="79539-196">glob パターンを `GetFileInfo` メソッドに渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="79539-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="79539-197">ファイル プロバイダーは、`applicationRoot` ディレクトリの外部にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="79539-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="79539-198">*FileProviderSample* サンプル アプリでは、<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> を使用して `Startup.ConfigureServices` メソッドにプロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="79539-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="79539-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="79539-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="79539-200"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> は、アセンブリに埋め込まれたファイルにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="79539-201">`ManifestEmbeddedFileProvider` では、アセンブリにコンパイルされたマニフェストを使用して、埋め込まれたファイルの元のパスを再構築します。</span><span class="sxs-lookup"><span data-stu-id="79539-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="79539-202">埋め込みファイルのマニフェストを生成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="79539-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="79539-203">[Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="79539-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="79539-204">`<GenerateEmbeddedFilesManifest>` プロパティを `true`に設定します。</span><span class="sxs-lookup"><span data-stu-id="79539-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="79539-205">[\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) を使用して列挙するファイルを指定します:</span><span class="sxs-lookup"><span data-stu-id="79539-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="79539-206">[glob パターン](#glob-patterns)を使用して、アセンブリに埋め込むファイルを 1 つまたは複数指定します。</span><span class="sxs-lookup"><span data-stu-id="79539-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="79539-207">*FileProviderSample* サンプル アプリでは `ManifestEmbeddedFileProvider` が作成され、現在実行しているアセンブリがそのコンストラクターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="79539-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="79539-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="79539-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="79539-209">追加のオーバーロードを使用すると、次のことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="79539-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="79539-210">相対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="79539-210">Specify a relative file path.</span></span>
* <span data-ttu-id="79539-211">ファイルのスコープを最終変更日に設定します。</span><span class="sxs-lookup"><span data-stu-id="79539-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="79539-212">埋め込みファイルのマニフェストを含む埋め込みリソースに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="79539-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="79539-213">オーバーロード</span><span class="sxs-lookup"><span data-stu-id="79539-213">Overload</span></span> | <span data-ttu-id="79539-214">説明</span><span class="sxs-lookup"><span data-stu-id="79539-214">Description</span></span> |
| ---
<span data-ttu-id="79539-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-216">'Blazor'</span></span>
- <span data-ttu-id="79539-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-217">'Identity'</span></span>
- <span data-ttu-id="79539-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-219">'Razor'</span></span>
- <span data-ttu-id="79539-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-222">'Blazor'</span></span>
- <span data-ttu-id="79539-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-223">'Identity'</span></span>
- <span data-ttu-id="79539-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-225">'Razor'</span></span>
- <span data-ttu-id="79539-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-226">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-228">'Blazor'</span></span>
- <span data-ttu-id="79539-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-229">'Identity'</span></span>
- <span data-ttu-id="79539-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-231">'Razor'</span></span>
- <span data-ttu-id="79539-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-234">'Blazor'</span></span>
- <span data-ttu-id="79539-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-235">'Identity'</span></span>
- <span data-ttu-id="79539-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-237">'Razor'</span></span>
- <span data-ttu-id="79539-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-240">'Blazor'</span></span>
- <span data-ttu-id="79539-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-241">'Identity'</span></span>
- <span data-ttu-id="79539-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-243">'Razor'</span></span>
- <span data-ttu-id="79539-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-244">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | 任意指定の `root` 相対パス パラメーターを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="79539-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="79539-246">`root` を指定して、<xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> の呼び出しのスコープを指定したパス以下のリソースに設定します。</span><span class="sxs-lookup"><span data-stu-id="79539-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="79539-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 任意指定の `root` 相対パス パラメーターと `lastModified` 日付 (<xref:System.DateTimeOffset>) パラメーターを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="79539-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="79539-248">`lastModified` の日付では、<xref:Microsoft.Extensions.FileProviders.IFileProvider> によって返される <xref:Microsoft.Extensions.FileProviders.IFileInfo> インスタンスの最終更新日のスコープを設定します。</span><span class="sxs-lookup"><span data-stu-id="79539-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="79539-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 任意指定の `root` 相対パス、`lastModified` 日付、および `manifestName` パラメーターを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="79539-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="79539-250">`manifestName` は、マニフェストを含む埋め込みリソースの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="79539-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="79539-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="79539-251">CompositeFileProvider</span></span>

<span data-ttu-id="79539-252"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> は、`IFileProvider` インスタンスを結合し、複数のプロバイダーからのファイルを操作するための 1 つのインターフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="79539-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="79539-253">`CompositeFileProvider` を作成する場合、1 つまたは複数の `IFileProvider` インスタンスをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="79539-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="79539-254">*FileProviderSample* サンプル アプリでは、`PhysicalFileProvider` と `ManifestEmbeddedFileProvider` により、アプリのサービス コンテナーに登録されている `CompositeFileProvider` にファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="79539-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="79539-255">次のコードは、プロジェクトの `Startup.ConfigureServices` メソッドにあります。</span><span class="sxs-lookup"><span data-stu-id="79539-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="79539-256">変更の監視</span><span class="sxs-lookup"><span data-stu-id="79539-256">Watch for changes</span></span>

<span data-ttu-id="79539-257"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> メソッドでは、変更がないかどうか 1 つ以上のファイルまたはディレクトリを監視するシナリオを提供します。</span><span class="sxs-lookup"><span data-stu-id="79539-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="79539-258">`Watch` メソッド:</span><span class="sxs-lookup"><span data-stu-id="79539-258">The `Watch` method:</span></span>

* <span data-ttu-id="79539-259">ファイル パス文字列を指定できます。これにより、[glob パターン](#glob-patterns)を使用して複数のファイルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="79539-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="79539-260"><xref:Microsoft.Extensions.Primitives.IChangeToken> を返します。</span><span class="sxs-lookup"><span data-stu-id="79539-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="79539-261">生成される変更トークンでは次のものが公開されます。</span><span class="sxs-lookup"><span data-stu-id="79539-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="79539-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>:このプロパティを調べることで、変更があったかどうかを判断できます。</span><span class="sxs-lookup"><span data-stu-id="79539-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="79539-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>:指定したパス文字列に対して変更が検出されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="79539-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="79539-264">各変更トークンは、1 つの変更への応答として、関連付けられたコールバックを呼び出すのみです。</span><span class="sxs-lookup"><span data-stu-id="79539-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="79539-265">定数の監視を有効にするには、<xref:System.Threading.Tasks.TaskCompletionSource`1> を使用するか (以下を参照)、変更への応答として `IChangeToken` インスタンスを再作成します。</span><span class="sxs-lookup"><span data-stu-id="79539-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="79539-266">*WatchConsole* サンプル アプリでは、*TextFiles* ディレクトリの *.txt* ファイルが変更されるたびに、メッセージが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="79539-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="79539-267">Docker コンテナーやネットワーク共有など、一部のファイル システムは、変更通知を確実に送信しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="79539-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="79539-268">`DOTNET_USE_POLLING_FILE_WATCHER` 環境変数を `1` または `true` に設定して、変更がないかどうか、4 秒ごとにファイル システムをポーリングして確認します (構成不可)。</span><span class="sxs-lookup"><span data-stu-id="79539-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="79539-269">glob パターン</span><span class="sxs-lookup"><span data-stu-id="79539-269">Glob patterns</span></span>

<span data-ttu-id="79539-270">ファイル システム パスは、"*glob (または globbing) パターン*" と呼ばれるワイルドカード パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="79539-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="79539-271">これらのパターンを使用して、ファイルのグループを指定します。</span><span class="sxs-lookup"><span data-stu-id="79539-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="79539-272">2 つのワイルドカード文字は、`*` と `**` です。</span><span class="sxs-lookup"><span data-stu-id="79539-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="79539-273">現在のフォルダー レベルにある任意の要素、任意のファイル名、または任意のファイル拡張子を照合します。</span><span class="sxs-lookup"><span data-stu-id="79539-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="79539-274">照合はファイル パス内の `/` 文字および `.` 文字によって終了します。</span><span class="sxs-lookup"><span data-stu-id="79539-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="79539-275">複数のディレクトリ レベルにわたって任意の要素を照合します。</span><span class="sxs-lookup"><span data-stu-id="79539-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="79539-276">ディレクトリ階層内の多数のファイルを再帰的に照合する場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="79539-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="79539-277">次の表は、glob パターンの一般的な例を示しています。</span><span class="sxs-lookup"><span data-stu-id="79539-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="79539-278">パターン</span><span class="sxs-lookup"><span data-stu-id="79539-278">Pattern</span></span>  |<span data-ttu-id="79539-279">説明</span><span class="sxs-lookup"><span data-stu-id="79539-279">Description</span></span>  |
|---
<span data-ttu-id="79539-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-281">'Blazor'</span></span>
- <span data-ttu-id="79539-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-282">'Identity'</span></span>
- <span data-ttu-id="79539-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-284">'Razor'</span></span>
- <span data-ttu-id="79539-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-287">'Blazor'</span></span>
- <span data-ttu-id="79539-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-288">'Identity'</span></span>
- <span data-ttu-id="79539-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-290">'Razor'</span></span>
- <span data-ttu-id="79539-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-291">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-293">'Blazor'</span></span>
- <span data-ttu-id="79539-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-294">'Identity'</span></span>
- <span data-ttu-id="79539-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-296">'Razor'</span></span>
- <span data-ttu-id="79539-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-299">'Blazor'</span></span>
- <span data-ttu-id="79539-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-300">'Identity'</span></span>
- <span data-ttu-id="79539-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-302">'Razor'</span></span>
- <span data-ttu-id="79539-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-303">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-304">-----|
|`directory/file.txt`|特定のディレクトリ内の特定のファイルと一致します。||`directory/*.txt`|特定のディレクトリ内で、拡張子が *.txt* のすべてのファイルと一致します。| |`directory/*/appsettings.json`|*directory* フォルダーの 1 つ下のレベルのディレクトリ内のすべての *appsettings.json* ファイルと一致します。| |`directory/**/*.txt`|*directory* フォルダーの下にある、拡張子が *.txt* のすべてのファイルと一致します。|</span><span class="sxs-lookup"><span data-stu-id="79539-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="79539-305">ASP.NET Core は、ファイル プロバイダーを使用してファイル システムへのアクセスを抽象化します。</span><span class="sxs-lookup"><span data-stu-id="79539-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="79539-306">ファイル プロバイダーは、ASP.NET Core フレームワークの全体で使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="79539-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> では、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)と [Web ルート](xref:fundamentals/index#web-root)が `IFileProvider` 型として公開されます。</span><span class="sxs-lookup"><span data-stu-id="79539-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="79539-308">[静的ファイル ミドルウェア](xref:fundamentals/static-files)では、ファイル プロバイダーを使用して静的なファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="79539-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="79539-309">[Razor](xref:mvc/views/razor) では、ファイル プロバイダーを使用してページとビューを見つけます。</span><span class="sxs-lookup"><span data-stu-id="79539-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="79539-310">.NET Core Tooling では、ファイル プロバイダーと glob パターンを使用して、公開するファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="79539-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="79539-311">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="79539-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="79539-312">ファイル プロバイダーのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="79539-312">File Provider interfaces</span></span>

<span data-ttu-id="79539-313">プライマリ インターフェイスは <xref:Microsoft.Extensions.FileProviders.IFileProvider> です。</span><span class="sxs-lookup"><span data-stu-id="79539-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="79539-314">`IFileProvider` では次のためのメソッドが公開されます。</span><span class="sxs-lookup"><span data-stu-id="79539-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="79539-315">ファイルの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="79539-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="79539-316">ディレクトリの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="79539-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="79539-317">変更通知を設定します (<xref:Microsoft.Extensions.Primitives.IChangeToken> を使用)。</span><span class="sxs-lookup"><span data-stu-id="79539-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="79539-318">`IFileInfo` ではファイルを操作するためのメソッドとプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="79539-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="79539-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (バイト単位)</span><span class="sxs-lookup"><span data-stu-id="79539-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="79539-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> の日付</span><span class="sxs-lookup"><span data-stu-id="79539-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="79539-321">[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) メソッドを使用してファイルから読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="79539-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="79539-322">サンプル アプリでは、[依存関係の挿入](xref:fundamentals/dependency-injection)を介してアプリ全体で使用するために、`Startup.ConfigureServices` でファイル プロバイダーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="79539-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="79539-323">ファイル プロバイダーの実装</span><span class="sxs-lookup"><span data-stu-id="79539-323">File Provider implementations</span></span>

<span data-ttu-id="79539-324">利用できる `IFileProvider` の実装は 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="79539-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="79539-325">実装</span><span class="sxs-lookup"><span data-stu-id="79539-325">Implementation</span></span> | <span data-ttu-id="79539-326">説明</span><span class="sxs-lookup"><span data-stu-id="79539-326">Description</span></span> |
| ---
<span data-ttu-id="79539-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-328">'Blazor'</span></span>
- <span data-ttu-id="79539-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-329">'Identity'</span></span>
- <span data-ttu-id="79539-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-331">'Razor'</span></span>
- <span data-ttu-id="79539-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-334">'Blazor'</span></span>
- <span data-ttu-id="79539-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-335">'Identity'</span></span>
- <span data-ttu-id="79539-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-337">'Razor'</span></span>
- <span data-ttu-id="79539-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-340">'Blazor'</span></span>
- <span data-ttu-id="79539-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-341">'Identity'</span></span>
- <span data-ttu-id="79539-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-343">'Razor'</span></span>
- <span data-ttu-id="79539-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-346">'Blazor'</span></span>
- <span data-ttu-id="79539-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-347">'Identity'</span></span>
- <span data-ttu-id="79539-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-349">'Razor'</span></span>
- <span data-ttu-id="79539-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-352">'Blazor'</span></span>
- <span data-ttu-id="79539-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-353">'Identity'</span></span>
- <span data-ttu-id="79539-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-355">'Razor'</span></span>
- <span data-ttu-id="79539-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-356">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-358">'Blazor'</span></span>
- <span data-ttu-id="79539-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-359">'Identity'</span></span>
- <span data-ttu-id="79539-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-361">'Razor'</span></span>
- <span data-ttu-id="79539-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-364">'Blazor'</span></span>
- <span data-ttu-id="79539-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-365">'Identity'</span></span>
- <span data-ttu-id="79539-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-367">'Razor'</span></span>
- <span data-ttu-id="79539-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-370">'Blazor'</span></span>
- <span data-ttu-id="79539-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-371">'Identity'</span></span>
- <span data-ttu-id="79539-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-373">'Razor'</span></span>
- <span data-ttu-id="79539-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-374">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | システムの物理ファイルにアクセスするために、物理プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="79539-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | アセンブリに埋め込まれているファイルにアクセスするために、マニフェストが埋め込まれたプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="79539-377">| | [CompositeFileProvider](#compositefileprovider) | その他の 1 つまたは複数のプロバイダーからのファイルおよびディレクトリに対するアクセスを結合するために、コンポジット プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="79539-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="79539-378">PhysicalFileProvider</span></span>

<span data-ttu-id="79539-379"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> は、物理ファイル システムへのアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="79539-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="79539-380">`PhysicalFileProvider` では、<xref:System.IO.File?displayProperty=fullName> 型が使用され (物理プロバイダーの場合)、すべてのパスのスコープが 1 つのディレクトリとその子ディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="79539-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="79539-381">このスコープ設定により、指定されたディレクトリとその子ディレクトリを除くファイル システムにアクセスできなくなります。</span><span class="sxs-lookup"><span data-stu-id="79539-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="79539-382">`PhysicalFileProvider` を作成して使用する最も一般的なシナリオは、[依存関係の挿入](xref:fundamentals/dependency-injection)を通してコンストラクターで `IFileProvider` を要求する場合です。</span><span class="sxs-lookup"><span data-stu-id="79539-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="79539-383">このプロバイダーを直接インスタンス化するときは、ディレクトリ パスが要求され、そのプロバイダーを使用して行われるすべての要求のベース パスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="79539-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="79539-384">次のコードでは、`PhysicalFileProvider` の作成方法と、これを使ってディレクトリのコンテンツとファイル情報を取得する方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="79539-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="79539-385">前の例の型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="79539-385">Types in the preceding example:</span></span>

* <span data-ttu-id="79539-386">`provider` は `IFileProvider` です。</span><span class="sxs-lookup"><span data-stu-id="79539-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="79539-387">`contents` は `IDirectoryContents` です。</span><span class="sxs-lookup"><span data-stu-id="79539-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="79539-388">`fileInfo` は `IFileInfo` です。</span><span class="sxs-lookup"><span data-stu-id="79539-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="79539-389">ファイル プロバイダーを使用して、`applicationRoot` で指定したディレクトリ全体を反復処理したり、`GetFileInfo` を呼び出してファイル情報を取得したりできます。</span><span class="sxs-lookup"><span data-stu-id="79539-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="79539-390">ファイル プロバイダーは、`applicationRoot` ディレクトリの外部にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="79539-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="79539-391">サンプル アプリの `Startup.ConfigureServices` クラスでは、[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) を使用してプロバイダーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="79539-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="79539-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="79539-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="79539-393"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> は、アセンブリに埋め込まれたファイルにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="79539-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="79539-394">`ManifestEmbeddedFileProvider` では、アセンブリにコンパイルされたマニフェストを使用して、埋め込まれたファイルの元のパスを再構築します。</span><span class="sxs-lookup"><span data-stu-id="79539-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="79539-395">埋め込みファイルのマニフェストを生成するには、`<GenerateEmbeddedFilesManifest>` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79539-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="79539-396">[&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) を使用して埋め込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="79539-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="79539-397">[glob パターン](#glob-patterns)を使用して、アセンブリに埋め込むファイルを 1 つまたは複数指定します。</span><span class="sxs-lookup"><span data-stu-id="79539-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="79539-398">サンプル アプリでは `ManifestEmbeddedFileProvider` を作成して、現在実行しているアセンブリをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="79539-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="79539-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="79539-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="79539-400">追加のオーバーロードを使用すると、次のことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="79539-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="79539-401">相対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="79539-401">Specify a relative file path.</span></span>
* <span data-ttu-id="79539-402">ファイルのスコープを最終変更日に設定します。</span><span class="sxs-lookup"><span data-stu-id="79539-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="79539-403">埋め込みファイルのマニフェストを含む埋め込みリソースに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="79539-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="79539-404">オーバーロード</span><span class="sxs-lookup"><span data-stu-id="79539-404">Overload</span></span> | <span data-ttu-id="79539-405">説明</span><span class="sxs-lookup"><span data-stu-id="79539-405">Description</span></span> |
| ---
<span data-ttu-id="79539-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-407">'Blazor'</span></span>
- <span data-ttu-id="79539-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-408">'Identity'</span></span>
- <span data-ttu-id="79539-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-410">'Razor'</span></span>
- <span data-ttu-id="79539-411">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-413">'Blazor'</span></span>
- <span data-ttu-id="79539-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-414">'Identity'</span></span>
- <span data-ttu-id="79539-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-416">'Razor'</span></span>
- <span data-ttu-id="79539-417">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-417">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-419">'Blazor'</span></span>
- <span data-ttu-id="79539-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-420">'Identity'</span></span>
- <span data-ttu-id="79539-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-422">'Razor'</span></span>
- <span data-ttu-id="79539-423">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-425">'Blazor'</span></span>
- <span data-ttu-id="79539-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-426">'Identity'</span></span>
- <span data-ttu-id="79539-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-428">'Razor'</span></span>
- <span data-ttu-id="79539-429">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79539-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79539-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79539-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79539-431">'Blazor'</span></span>
- <span data-ttu-id="79539-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79539-432">'Identity'</span></span>
- <span data-ttu-id="79539-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79539-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="79539-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79539-434">'Razor'</span></span>
- <span data-ttu-id="79539-435">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79539-435">'SignalR' uid:</span></span> 

<span data-ttu-id="79539-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | 任意指定の `root` 相対パス パラメーターを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="79539-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="79539-437">`root` を指定して、<xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> の呼び出しのスコープを指定したパス以下のリソースに設定します。</span><span class="sxs-lookup"><span data-stu-id="79539-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="79539-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 任意指定の `root` 相対パス パラメーターと `lastModified` 日付 (<xref:System.DateTimeOffset>) パラメーターを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="79539-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="79539-439">`lastModified` の日付では、<xref:Microsoft.Extensions.FileProviders.IFileProvider> によって返される <xref:Microsoft.Extensions.FileProviders.IFileInfo> インスタンスの最終更新日のスコープを設定します。</span><span class="sxs-lookup"><span data-stu-id="79539-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="79539-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 任意指定の `root` 相対パス、`lastModified` 日付、および `manifestName` パラメーターを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="79539-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="79539-441">`manifestName` は、マニフェストを含む埋め込みリソースの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="79539-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="79539-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="79539-442">CompositeFileProvider</span></span>

<span data-ttu-id="79539-443"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> は、`IFileProvider` インスタンスを結合し、複数のプロバイダーからのファイルを操作するための 1 つのインターフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="79539-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="79539-444">`CompositeFileProvider` を作成する場合、1 つまたは複数の `IFileProvider` インスタンスをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="79539-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="79539-445">サンプル アプリでは、`PhysicalFileProvider` と `ManifestEmbeddedFileProvider` が、アプリのサービス コンテナーに登録されている `CompositeFileProvider` にファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="79539-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="79539-446">変更の監視</span><span class="sxs-lookup"><span data-stu-id="79539-446">Watch for changes</span></span>

<span data-ttu-id="79539-447">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) メソッドによって、1 つまたは複数のファイルやディレクトリに変更がないかどうか監視するシナリオが提供されます。</span><span class="sxs-lookup"><span data-stu-id="79539-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="79539-448">`Watch` にはパス文字列を指定できます。ここでは、[glob パターン](#glob-patterns)を使用して複数のファイルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="79539-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="79539-449">`Watch` では <xref:Microsoft.Extensions.Primitives.IChangeToken> が返されます。</span><span class="sxs-lookup"><span data-stu-id="79539-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="79539-450">変更トークンでは次のものが公開されます。</span><span class="sxs-lookup"><span data-stu-id="79539-450">The change token exposes:</span></span>

* <span data-ttu-id="79539-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>:このプロパティを調べることで、変更があったかどうかを判断できます。</span><span class="sxs-lookup"><span data-stu-id="79539-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="79539-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>:指定したパス文字列に対して変更が検出されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="79539-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="79539-453">各変更トークンは、1 つの変更への応答として、関連付けられたコールバックを呼び出すのみです。</span><span class="sxs-lookup"><span data-stu-id="79539-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="79539-454">定数の監視を有効にするには、<xref:System.Threading.Tasks.TaskCompletionSource`1> を使用するか (以下を参照)、変更への応答として `IChangeToken` インスタンスを再作成します。</span><span class="sxs-lookup"><span data-stu-id="79539-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="79539-455">サンプル アプリでは、*WatchConsole* コンソール アプリは、テキスト ファイルが変更されるたびにメッセージを表示するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="79539-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="79539-456">Docker コンテナーやネットワーク共有など、一部のファイル システムは、変更通知を確実に送信しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="79539-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="79539-457">`DOTNET_USE_POLLING_FILE_WATCHER` 環境変数を `1` または `true` に設定して、変更がないかどうか、4 秒ごとにファイル システムをポーリングして確認します (構成不可)。</span><span class="sxs-lookup"><span data-stu-id="79539-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="79539-458">glob パターン</span><span class="sxs-lookup"><span data-stu-id="79539-458">Glob patterns</span></span>

<span data-ttu-id="79539-459">ファイル システム パスは、"*glob (または globbing) パターン*" と呼ばれるワイルドカード パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="79539-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="79539-460">これらのパターンを使用して、ファイルのグループを指定します。</span><span class="sxs-lookup"><span data-stu-id="79539-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="79539-461">2 つのワイルドカード文字は、`*` と `**` です。</span><span class="sxs-lookup"><span data-stu-id="79539-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="79539-462">現在のフォルダー レベルにある任意の要素、任意のファイル名、または任意のファイル拡張子を照合します。</span><span class="sxs-lookup"><span data-stu-id="79539-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="79539-463">照合はファイル パス内の `/` 文字および `.` 文字によって終了します。</span><span class="sxs-lookup"><span data-stu-id="79539-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="79539-464">複数のディレクトリ レベルにわたって任意の要素を照合します。</span><span class="sxs-lookup"><span data-stu-id="79539-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="79539-465">ディレクトリ階層内の多数のファイルを再帰的に照合する場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="79539-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="79539-466">**glob パターンの例**</span><span class="sxs-lookup"><span data-stu-id="79539-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="79539-467">特定のディレクトリ内の特定のファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="79539-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="79539-468">特定のディレクトリ内の *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="79539-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="79539-469">*directory* フォルダーのちょうど 1 つ下のレベルにあるディレクトリ内のすべての `appsettings.json` ファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="79539-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="79539-470">*directory* フォルダーの下の任意の場所にある、 *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="79539-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
