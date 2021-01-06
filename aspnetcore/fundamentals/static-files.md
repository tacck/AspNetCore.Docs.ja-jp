---
title: ASP.NET Core の静的ファイル
author: rick-anderson
description: 静的ファイルを提供したり、それをセキュリティで保護したりする方法、および ASP.NET Core Web アプリで静的ファイルをホストするミドルウェアの動作を構成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 2e25af03a8a6aaff5b343885711c6ebb68340fac
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057857"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="859ee-103">ASP.NET Core の静的ファイル</span><span class="sxs-lookup"><span data-stu-id="859ee-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="859ee-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="859ee-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="859ee-105">HTML、CSS、画像、JavaScript などの静的ファイルは、既定では ASP.NET Core アプリにより直接クライアントに提供される資産です。</span><span class="sxs-lookup"><span data-stu-id="859ee-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="859ee-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="859ee-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="859ee-107">静的ファイルの提供</span><span class="sxs-lookup"><span data-stu-id="859ee-107">Serve static files</span></span>

<span data-ttu-id="859ee-108">静的ファイルは、プロジェクトの [Web ルート](xref:fundamentals/index#web-root) ディレクトリ内に格納されています。</span><span class="sxs-lookup"><span data-stu-id="859ee-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="859ee-109">既定のディレクトリは `{content root}/wwwroot` ですが、<xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> メソッドを使用して変更できます。</span><span class="sxs-lookup"><span data-stu-id="859ee-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="859ee-110">詳細については、「[コンテンツ ルート](xref:fundamentals/index#content-root)」および「[Web ルート](xref:fundamentals/index#web-root)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="859ee-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="859ee-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> メソッドでは、コンテンツのルートが現在のディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="859ee-112">上記のコードは、Web アプリ テンプレートを使用して作成されました。</span><span class="sxs-lookup"><span data-stu-id="859ee-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="859ee-113">静的ファイルには、[Web ルート](xref:fundamentals/index#web-root)に対する相対パスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="859ee-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="859ee-114">たとえば、**Web アプリケーション** プロジェクト テンプレートでは、`wwwroot` フォルダー内に次のいくつかのフォルダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="859ee-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="859ee-115">*wwwroot/images* フォルダーを作成し、*wwwroot/images/MyImage.jpg* ファイルを追加する場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="859ee-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="859ee-116">`images` フォルダー内のファイルにアクセスするための URI 形式は `https://<hostname>/images/<image_file_name>` です。</span><span class="sxs-lookup"><span data-stu-id="859ee-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="859ee-117">たとえば、`https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="859ee-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="859ee-118">Web ルート内のファイルの提供</span><span class="sxs-lookup"><span data-stu-id="859ee-118">Serve files in web root</span></span>

<span data-ttu-id="859ee-119">既定の Web アプリ テンプレートでは、`Startup.Configure` 内で <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> メソッドが呼び出されます。これにより、静的ファイルを提供できるようになります。</span><span class="sxs-lookup"><span data-stu-id="859ee-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="859ee-120">`UseStaticFiles` メソッドのパラメーターなしのオーバーロードによって、[Web ルート](xref:fundamentals/index#web-root)内のファイルが提供可能とマークされます。</span><span class="sxs-lookup"><span data-stu-id="859ee-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="859ee-121">次のマークアップは、*wwwroot/images/MyImage.jpg* を参照します。</span><span class="sxs-lookup"><span data-stu-id="859ee-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="859ee-122">上記のコードでは、チルダ文字 `~/` が [Web ルート](xref:fundamentals/index#web-root)を指します。</span><span class="sxs-lookup"><span data-stu-id="859ee-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="859ee-123">Web ルート外のファイルの提供</span><span class="sxs-lookup"><span data-stu-id="859ee-123">Serve files outside of web root</span></span>

<span data-ttu-id="859ee-124">提供する静的ファイルが [Web ルート](xref:fundamentals/index#web-root)の外にあるディレクトリ階層について考えます。</span><span class="sxs-lookup"><span data-stu-id="859ee-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="859ee-125">静的ファイル ミドルウェアを次のように構成すると、要求で `red-rose.jpg` ファイルにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="859ee-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="859ee-126">前述のコードでは、*MyStaticFiles* ディレクトリ階層は、*StaticFiles* URI セグメントでパブリックに公開されています。</span><span class="sxs-lookup"><span data-stu-id="859ee-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="859ee-127">`https://<hostname>/StaticFiles/images/red-rose.jpg` の要求は、*red-rose.jpg* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="859ee-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="859ee-128">次のマークアップは、*MyStaticFiles/images/red-rose.jpg* を参照します。</span><span class="sxs-lookup"><span data-stu-id="859ee-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="859ee-129">HTTP 応答ヘッダーの設定</span><span class="sxs-lookup"><span data-stu-id="859ee-129">Set HTTP response headers</span></span>

<span data-ttu-id="859ee-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> オブジェクトを使用すると、HTTP 応答ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="859ee-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="859ee-131">[Web ルート](xref:fundamentals/index#web-root)から提供される静的ファイルの構成に加えて、次のコードで `Cache-Control` ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="859ee-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="859ee-132">以下のように、静的ファイルは 600 秒間パブリックにキャッシュ可能です。</span><span class="sxs-lookup"><span data-stu-id="859ee-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Cache-Control ヘッダーが追加された応答ヘッダー](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="859ee-134">静的ファイルの承認</span><span class="sxs-lookup"><span data-stu-id="859ee-134">Static file authorization</span></span>

<span data-ttu-id="859ee-135">静的ファイル ミドルウェアでは、承認の確認は行いません。</span><span class="sxs-lookup"><span data-stu-id="859ee-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="859ee-136">`wwwroot` の下にあるものも含め、このミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="859ee-137">承認に基づいてファイルを提供するには:</span><span class="sxs-lookup"><span data-stu-id="859ee-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="859ee-138">`wwwroot` や既定の静的ファイル ミドルウェアがアクセスできる任意のディレクトリの外にそれらを配置します。</span><span class="sxs-lookup"><span data-stu-id="859ee-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="859ee-139">`UseAuthorization` の後に `UseStaticFiles` を呼び出し、パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="859ee-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="859ee-140">上記の方法では、ユーザーの認証が必要です。</span><span class="sxs-lookup"><span data-stu-id="859ee-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="859ee-141">承認に基づいてファイルを提供する別の方法:</span><span class="sxs-lookup"><span data-stu-id="859ee-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="859ee-142">`wwwroot` や静的ファイル ミドルウェアがアクセスできる任意のディレクトリの外にファイルを配置します。</span><span class="sxs-lookup"><span data-stu-id="859ee-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="859ee-143">承認が適用されるアクション メソッドを使用して提供し、<xref:Microsoft.AspNetCore.Mvc.FileResult> オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="859ee-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="859ee-144">ディレクトリ参照</span><span class="sxs-lookup"><span data-stu-id="859ee-144">Directory browsing</span></span>

<span data-ttu-id="859ee-145">ディレクトリ参照を使用すると、指定したディレクトリ内のディレクトリを一覧表示できます。</span><span class="sxs-lookup"><span data-stu-id="859ee-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="859ee-146">ディレクトリ参照は、セキュリティ上の理由から既定で無効になっています。</span><span class="sxs-lookup"><span data-stu-id="859ee-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="859ee-147">詳細については、「[注意事項](#considerations)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="859ee-147">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="859ee-148">ディレクトリ参照を有効にするには、次のメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="859ee-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="859ee-149">`Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A></span><span class="sxs-lookup"><span data-stu-id="859ee-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="859ee-150">`Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A></span><span class="sxs-lookup"><span data-stu-id="859ee-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="859ee-151">上記のコードでは、URL `https://<hostname>/MyImages` が使用され、各ファイルおよびフォルダーへのリンクを含む *wwwroot/images* フォルダーのディレクトリが参照できるようになります。</span><span class="sxs-lookup"><span data-stu-id="859ee-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![ディレクトリ参照](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="859ee-153">既定のドキュメントの提供</span><span class="sxs-lookup"><span data-stu-id="859ee-153">Serve default documents</span></span>

<span data-ttu-id="859ee-154">既定のページを設定すると、サイトのビジターの開始点になります。</span><span class="sxs-lookup"><span data-stu-id="859ee-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="859ee-155">完全修飾 URI を使用せずに `wwwroot` から既定のページを提供するには、<xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="859ee-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="859ee-156">既定のファイルを提供するには、`UseStaticFiles` の前に `UseDefaultFiles` が呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="859ee-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="859ee-157">`UseDefaultFiles` は、ファイルを提供しない URL リライターです。</span><span class="sxs-lookup"><span data-stu-id="859ee-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="859ee-158">`UseDefaultFiles` を使用すると、`wwwroot` 内のフォルダーの要求では以下のファイルが検索されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="859ee-159">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="859ee-159">*default.htm*</span></span>
* <span data-ttu-id="859ee-160">*default.html*</span><span class="sxs-lookup"><span data-stu-id="859ee-160">*default.html*</span></span>
* <span data-ttu-id="859ee-161">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="859ee-161">*index.htm*</span></span>
* <span data-ttu-id="859ee-162">*index.html*</span><span class="sxs-lookup"><span data-stu-id="859ee-162">*index.html*</span></span>

<span data-ttu-id="859ee-163">一覧で見つかった最初のファイルは、要求で完全修飾 URI が使用されたかのように提供されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="859ee-164">ブラウザー URL は、要求された URI を反映し続けます。</span><span class="sxs-lookup"><span data-stu-id="859ee-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="859ee-165">次のコードによって、既定のファイル名が *mydefault.html* に変更されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-165">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="859ee-166">次のコードでは、上記のコードを含めた `Startup.Configure` を示します。</span><span class="sxs-lookup"><span data-stu-id="859ee-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="859ee-167">既定のドキュメントの UseFileServer</span><span class="sxs-lookup"><span data-stu-id="859ee-167">UseFileServer for default documents</span></span>

<span data-ttu-id="859ee-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> は、`UseStaticFiles`、`UseDefaultFiles`、およびオプションとして `UseDirectoryBrowser` の機能を兼ね備えています。</span><span class="sxs-lookup"><span data-stu-id="859ee-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="859ee-169">`app.UseFileServer` を呼び出すと、静的ファイルと既定ファイルが提供できるようになります。</span><span class="sxs-lookup"><span data-stu-id="859ee-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="859ee-170">ディレクトリ参照は有効にしません。</span><span class="sxs-lookup"><span data-stu-id="859ee-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="859ee-171">次のコードでは、`UseFileServer` を含めた `Startup.Configure` を示します。</span><span class="sxs-lookup"><span data-stu-id="859ee-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="859ee-172">次のコードによって、静的ファイルの提供、既定ファイル、ディレクトリ参照が有効になります。</span><span class="sxs-lookup"><span data-stu-id="859ee-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="859ee-173">次のコードでは、上記のコードを含めた `Startup.Configure` を示します。</span><span class="sxs-lookup"><span data-stu-id="859ee-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="859ee-174">次のディレクトリ階層があるとします。</span><span class="sxs-lookup"><span data-stu-id="859ee-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="859ee-175">次のコードによって、`MyStaticFiles` の静的ファイルの提供、既定ファイル、ディレクトリ参照が有効になります。</span><span class="sxs-lookup"><span data-stu-id="859ee-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="859ee-176">`EnableDirectoryBrowsing` プロパティの値が `true` であるときは、<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="859ee-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="859ee-177">このファイル階層と前述のコードを使用すると、URL は次のように解決されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="859ee-178">URI</span><span class="sxs-lookup"><span data-stu-id="859ee-178">URI</span></span>            |      <span data-ttu-id="859ee-179">応答</span><span class="sxs-lookup"><span data-stu-id="859ee-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="859ee-180">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="859ee-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="859ee-181">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="859ee-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="859ee-182">*MyStaticFiles* ディレクトリに既定の名前のファイルが存在しない場合、`https://<hostname>/StaticFiles` によってクリック可能なリンクを含むディレクトリの一覧が返されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![静的ファイルの一覧](static-files/_static/db2.png)

<span data-ttu-id="859ee-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> および <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> では、末尾の `/` がないターゲット URI から末尾の `/` があるターゲット URI へのクライアント側リダイレクトが実行されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="859ee-185">たとえば、`https://<hostname>/StaticFiles` は `https://<hostname>/StaticFiles/` になります。</span><span class="sxs-lookup"><span data-stu-id="859ee-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="859ee-186">*StaticFiles* ディレクトリ内の相対 URL は、末尾のスラッシュ (`/`) がないと無効です。</span><span class="sxs-lookup"><span data-stu-id="859ee-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="859ee-187">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="859ee-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="859ee-188"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> クラスには、MIME コンテンツ タイプへのファイル拡張子のマッピングを行う `Mappings` プロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="859ee-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="859ee-189">次の例では、いくつかのファイル拡張子が、既知の MIME タイプにマッピングされています。</span><span class="sxs-lookup"><span data-stu-id="859ee-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="859ee-190">*.rtf* 拡張子は置換され、 *.mp4* は削除されています。</span><span class="sxs-lookup"><span data-stu-id="859ee-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="859ee-191">次のコードでは、上記のコードを含めた `Startup.Configure` を示します。</span><span class="sxs-lookup"><span data-stu-id="859ee-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="859ee-192">「[MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml)」 (MIME コンテンツ タイプ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="859ee-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="859ee-193">非標準のコンテンツ タイプ</span><span class="sxs-lookup"><span data-stu-id="859ee-193">Non-standard content types</span></span>

<span data-ttu-id="859ee-194">静的ファイル ミドルウェアでは、約 400 の既知のファイル コンテンツ タイプが認識されています。</span><span class="sxs-lookup"><span data-stu-id="859ee-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="859ee-195">ユーザーがファイルの種類が不明なファイルを要求した場合、静的ファイル ミドルウェアでその要求がパイプラインの次のミドルウェアに渡されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="859ee-196">ミドルウェアで要求が処理されない場合、*404 見つかりません* という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="859ee-197">ディレクトリ参照が有効になっている場合、ディレクトリ一覧にファイルへのリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="859ee-198">次のコードによって、不明なタイプを提供できるようにし、不明なファイルをイメージとしてレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="859ee-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="859ee-199">次のコードでは、上記のコードを含めた `Startup.Configure` を示します。</span><span class="sxs-lookup"><span data-stu-id="859ee-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="859ee-200">上記のコードでは、不明なコンテンツ タイプ ファイルに対する要求は、イメージとして返されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="859ee-201"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> を有効にすると、セキュリティ上リスクとなります。</span><span class="sxs-lookup"><span data-stu-id="859ee-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="859ee-202">これは既定では無効で、使用は推奨されていません。</span><span class="sxs-lookup"><span data-stu-id="859ee-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="859ee-203">非標準の拡張子のファイルを提供する場合、より安全な代替となるのは、[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) です。</span><span class="sxs-lookup"><span data-stu-id="859ee-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="859ee-204">複数の場所からファイルを提供する</span><span class="sxs-lookup"><span data-stu-id="859ee-204">Serve files from multiple locations</span></span>

<span data-ttu-id="859ee-205">`UseStaticFiles` と `UseFileServer` の既定では、`wwwroot` をポイントするファイル プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="859ee-206">`UseStaticFiles` および `UseFileServer` の追加インスタンスを作成して他のファイル プロバイダーを使用すると、他の場所からファイルを提供することができます。</span><span class="sxs-lookup"><span data-stu-id="859ee-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="859ee-207">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/15578)します。</span><span class="sxs-lookup"><span data-stu-id="859ee-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="859ee-208">静的ファイルのセキュリティに関する注意点</span><span class="sxs-lookup"><span data-stu-id="859ee-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="859ee-209">`UseDirectoryBrowser` と `UseStaticFiles` では、機密データが漏洩することがあります。</span><span class="sxs-lookup"><span data-stu-id="859ee-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="859ee-210">本番では、ディレクトリ参照を無効にすることが、強く推奨されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="859ee-211">`UseStaticFiles` や `UseDirectoryBrowser` でどのディレクトリが有効になっているか、慎重にご確認ください。</span><span class="sxs-lookup"><span data-stu-id="859ee-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="859ee-212">ディレクトリ全体とそのサブディレクトリが、パブリックにアクセス可能になります。</span><span class="sxs-lookup"><span data-stu-id="859ee-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="859ee-213">ファイルは、パブリックに提供するのに適した、`<content_root>/wwwroot` などの専用ディレクトリに格納します。</span><span class="sxs-lookup"><span data-stu-id="859ee-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="859ee-214">これらのファイルは、MVC ビュー、Razor Pages、構成ファイルなどとは別にします。</span><span class="sxs-lookup"><span data-stu-id="859ee-214">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="859ee-215">`UseDirectoryBrowser` と `UseStaticFiles` で公開されるコンテンツの URL では、大文字と小文字が区別され、基になるファイル システムの文字制限の影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="859ee-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="859ee-216">たとえば、Windows では大文字と小文字が区別されませんが、macOS と Linux では区別されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="859ee-217">IIS でホストされている ASP.NET Core アプリは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を使用して、静的ファイルの要求を含む、すべての要求をアプリに転送します。</span><span class="sxs-lookup"><span data-stu-id="859ee-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="859ee-218">IIS の静的ファイル ハンドラーは使用されず、要求を処理することはできません。</span><span class="sxs-lookup"><span data-stu-id="859ee-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="859ee-219">IIS マネージャーで次の手順を実行し、サーバーまたは Web サイト レベルで IIS の静的ファイル ハンドラーを削除します。</span><span class="sxs-lookup"><span data-stu-id="859ee-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="859ee-220">**[モジュール]** 機能に移動します。</span><span class="sxs-lookup"><span data-stu-id="859ee-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="859ee-221">一覧の **[StaticFileModule]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="859ee-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="859ee-222">**[アクション]** サイドバーで、 **[削除]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="859ee-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="859ee-223">IIS の静的ファイル ハンドラーが有効になっており、**かつ**、ASP.NET Core モジュールが正しく構成されていない場合、静的ファイルにサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="859ee-224">これは、たとえば、*web.config* ファイルが配置されていない場合などで発生します。</span><span class="sxs-lookup"><span data-stu-id="859ee-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="859ee-225">アプリ プロジェクトの [Web ルート](xref:fundamentals/index#web-root)の外に、コード ファイル ( *.cs* と *.cshtml* を含む) を配置します。</span><span class="sxs-lookup"><span data-stu-id="859ee-225">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="859ee-226">これにより、アプリのクライアント側コンテンツとサーバー ベースのコードの間で、論理的な分離が作成されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="859ee-227">これによって、サーバー側のコードが漏洩するのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="859ee-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="859ee-228">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="859ee-228">Additional resources</span></span>

* [<span data-ttu-id="859ee-229">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="859ee-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="859ee-230">ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="859ee-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="859ee-231">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="859ee-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="859ee-232">HTML、CSS、画像、JavaScript などの静的ファイルは、ASP.NET Core アプリにより直接クライアントに提供される資産です。</span><span class="sxs-lookup"><span data-stu-id="859ee-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="859ee-233">これらのファイルを提供するには、いくつかの構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="859ee-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="859ee-234">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="859ee-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="859ee-235">静的ファイルの提供</span><span class="sxs-lookup"><span data-stu-id="859ee-235">Serve static files</span></span>

<span data-ttu-id="859ee-236">静的ファイルは、プロジェクトの [Web ルート](xref:fundamentals/index#web-root) ディレクトリ内に格納されています。</span><span class="sxs-lookup"><span data-stu-id="859ee-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="859ee-237">既定のディレクトリは *{content root}/wwwroot* ですが、<xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> メソッドを使用して変更できます。</span><span class="sxs-lookup"><span data-stu-id="859ee-237">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="859ee-238">詳細については、「[コンテンツ ルート](xref:fundamentals/index#content-root)」および「[Web ルート](xref:fundamentals/index#web-root)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="859ee-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="859ee-239">アプリの Web ホストでは、コンテンツのルート ディレクトリが把握されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="859ee-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="859ee-240">`WebHost.CreateDefaultBuilder` メソッドでは、コンテンツのルートが現在のディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="859ee-241">静的ファイルには、[Web ルート](xref:fundamentals/index#web-root)に対する相対パスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="859ee-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="859ee-242">たとえば、**Web アプリケーション** プロジェクト テンプレートでは、`wwwroot` フォルダー内に次のいくつかのフォルダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="859ee-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="859ee-243">*images* サブフォルダーのファイルにアクセスするための URI は、*http://\<server_address>/images/\<image_file_name>* です。</span><span class="sxs-lookup"><span data-stu-id="859ee-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="859ee-244">たとえば、 *http://localhost:9189/images/banner3.svg* です。</span><span class="sxs-lookup"><span data-stu-id="859ee-244">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="859ee-245">.NET Framework を対象としている場合、プロジェクトに [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="859ee-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="859ee-246">.NET Core をターゲットとする場合、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)にこのパッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="859ee-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="859ee-247">静的ファイル サービスの提供を可能にする、[ミドルウェア](xref:fundamentals/middleware/index)を構成します。</span><span class="sxs-lookup"><span data-stu-id="859ee-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="859ee-248">Web ルート内のファイルの提供</span><span class="sxs-lookup"><span data-stu-id="859ee-248">Serve files inside of web root</span></span>

<span data-ttu-id="859ee-249">`Startup.Configure` 内で <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="859ee-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="859ee-250">`UseStaticFiles` メソッドのパラメーターなしのオーバーロードによって、[Web ルート](xref:fundamentals/index#web-root)内のファイルが提供可能とマークされます。</span><span class="sxs-lookup"><span data-stu-id="859ee-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="859ee-251">次のマークアップは、*wwwroot/images/banner1.svg* を参照します。</span><span class="sxs-lookup"><span data-stu-id="859ee-251">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="859ee-252">上記のコードでは、チルダ文字 `~/` が [Web ルート](xref:fundamentals/index#web-root)を指します。</span><span class="sxs-lookup"><span data-stu-id="859ee-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="859ee-253">Web ルート外のファイルの提供</span><span class="sxs-lookup"><span data-stu-id="859ee-253">Serve files outside of web root</span></span>

<span data-ttu-id="859ee-254">提供する静的ファイルが [Web ルート](xref:fundamentals/index#web-root)の外にあるディレクトリ階層について考えます。</span><span class="sxs-lookup"><span data-stu-id="859ee-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="859ee-255">静的ファイル ミドルウェアを次のように構成すると、要求で *banner1.svg* ファイルにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="859ee-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="859ee-256">前述のコードでは、*MyStaticFiles* ディレクトリ階層は、*StaticFiles* URI セグメントでパブリックに公開されています。</span><span class="sxs-lookup"><span data-stu-id="859ee-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="859ee-257">*http://\<server_address>/StaticFiles/images/banner1.svg* に対する要求によって、*banner1.svg* ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="859ee-258">次のマークアップは、*MyStaticFiles/images/banner1.svg* を参照します。</span><span class="sxs-lookup"><span data-stu-id="859ee-258">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="859ee-259">HTTP 応答ヘッダーの設定</span><span class="sxs-lookup"><span data-stu-id="859ee-259">Set HTTP response headers</span></span>

<span data-ttu-id="859ee-260"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> オブジェクトを使用すると、HTTP 応答ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="859ee-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="859ee-261">[Web ルート](xref:fundamentals/index#web-root)から提供される静的ファイルの構成に加えて、次のコードで `Cache-Control` ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="859ee-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="859ee-262"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> メソッドは、[Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="859ee-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="859ee-263">これらのファイルは、開発環境で 10 分間 (600 秒) パブリックにキャッシュ可能でした。</span><span class="sxs-lookup"><span data-stu-id="859ee-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Cache-Control ヘッダーが追加された応答ヘッダー](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="859ee-265">静的ファイルの承認</span><span class="sxs-lookup"><span data-stu-id="859ee-265">Static file authorization</span></span>

<span data-ttu-id="859ee-266">静的ファイル ミドルウェアでは、承認の確認は行いません。</span><span class="sxs-lookup"><span data-stu-id="859ee-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="859ee-267">*wwwroot* の下にあるものも含め、このミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-267">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="859ee-268">承認に基づいてファイルを提供するには:</span><span class="sxs-lookup"><span data-stu-id="859ee-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="859ee-269">*wwwroot* や静的ファイル ミドルウェアがアクセスできる任意のディレクトリの外にファイルを配置します。</span><span class="sxs-lookup"><span data-stu-id="859ee-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="859ee-270">承認が適用されるアクション メソッドを使用して提供するようにします。</span><span class="sxs-lookup"><span data-stu-id="859ee-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="859ee-271"><xref:Microsoft.AspNetCore.Mvc.FileResult> オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="859ee-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="859ee-272">ディレクトリ参照の有効化</span><span class="sxs-lookup"><span data-stu-id="859ee-272">Enable directory browsing</span></span>

<span data-ttu-id="859ee-273">ディレクトリ参照では、Web アプリのユーザーが、ディレクトリ一覧および、指定したディレクトリ内のファイルを参照できるようになります。</span><span class="sxs-lookup"><span data-stu-id="859ee-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="859ee-274">ディレクトリ参照は、セキュリティ上の理由から既定で無効になっています (「[注意点](#considerations)」を参照)。</span><span class="sxs-lookup"><span data-stu-id="859ee-274">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="859ee-275">`Startup.Configure` で <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> メソッドを呼び出すことで、ディレクトリの参照を有効にします。</span><span class="sxs-lookup"><span data-stu-id="859ee-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="859ee-276">`Startup.ConfigureServices` から <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> メソッドを呼び出すと、必要なサービスを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="859ee-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="859ee-277">上記のコードでは、URL *http://\<server_address>/MyImages* が使用され、各ファイルおよびフォルダーへのリンクを含む *wwwroot/images* フォルダーのディレクトリが参照できるようになります。</span><span class="sxs-lookup"><span data-stu-id="859ee-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![ディレクトリ参照](static-files/_static/dir-browse.png)

<span data-ttu-id="859ee-279">参照を有効にした場合のセキュリティ上のリスクについては、「[注意点](#considerations)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="859ee-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="859ee-280">次の例の 2 つの `UseStaticFiles` 呼び出しを確認してください。</span><span class="sxs-lookup"><span data-stu-id="859ee-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="859ee-281">最初の呼び出しにより、*wwwroot* フォルダー内の静的ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="859ee-282">2 番目の呼び出しにより、URL *http://\<server_address>/MyImages* が使用され、*wwwroot/images* フォルダーのディレクトリ参照が有効になります。</span><span class="sxs-lookup"><span data-stu-id="859ee-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="859ee-283">既定のドキュメントの提供</span><span class="sxs-lookup"><span data-stu-id="859ee-283">Serve a default document</span></span>

<span data-ttu-id="859ee-284">設定された既定のホーム ページは、サイトを訪問するユーザーの論理的な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="859ee-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="859ee-285">ユーザーが URI を完全修飾しない場合に既定のページを提供するには、`Startup.Configure` から <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="859ee-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="859ee-286">既定のファイルを提供するには、`UseStaticFiles` の前に `UseDefaultFiles` が呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="859ee-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="859ee-287">`UseDefaultFiles` は、ファイルを実際には提供しない URL リライターです。</span><span class="sxs-lookup"><span data-stu-id="859ee-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="859ee-288">ファイルを提供するには、`UseStaticFiles` を使用して静的ファイル ミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="859ee-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="859ee-289">`UseDefaultFiles` を使用し、以下のフォルダーを検索します。</span><span class="sxs-lookup"><span data-stu-id="859ee-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="859ee-290">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="859ee-290">*default.htm*</span></span>
* <span data-ttu-id="859ee-291">*default.html*</span><span class="sxs-lookup"><span data-stu-id="859ee-291">*default.html*</span></span>
* <span data-ttu-id="859ee-292">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="859ee-292">*index.htm*</span></span>
* <span data-ttu-id="859ee-293">*index.html*</span><span class="sxs-lookup"><span data-stu-id="859ee-293">*index.html*</span></span>

<span data-ttu-id="859ee-294">一覧で見つかった最初のファイルは、要求で完全修飾 URI が使用されたかのように提供されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="859ee-295">ブラウザー URL は、要求された URI を反映し続けます。</span><span class="sxs-lookup"><span data-stu-id="859ee-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="859ee-296">次のコードによって、既定のファイル名が *mydefault.html* に変更されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-296">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="859ee-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="859ee-297">UseFileServer</span></span>

<span data-ttu-id="859ee-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> は、`UseStaticFiles`、`UseDefaultFiles`、およびオプションとして `UseDirectoryBrowser` の機能を兼ね備えています。</span><span class="sxs-lookup"><span data-stu-id="859ee-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="859ee-299">次のコードによって、静的ファイルと既定ファイルが提供できるようになります。</span><span class="sxs-lookup"><span data-stu-id="859ee-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="859ee-300">ディレクトリ参照は有効にしません。</span><span class="sxs-lookup"><span data-stu-id="859ee-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="859ee-301">次のコードは、ディレクトリ参照が有効になった、パラメーターなしのオーバーロード時に構築されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="859ee-302">次のディレクトリ階層があるとします。</span><span class="sxs-lookup"><span data-stu-id="859ee-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="859ee-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="859ee-303">**wwwroot**</span></span>
  * <span data-ttu-id="859ee-304">**css**</span><span class="sxs-lookup"><span data-stu-id="859ee-304">**css**</span></span>
  * <span data-ttu-id="859ee-305">**images**</span><span class="sxs-lookup"><span data-stu-id="859ee-305">**images**</span></span>
  * <span data-ttu-id="859ee-306">**js**</span><span class="sxs-lookup"><span data-stu-id="859ee-306">**js**</span></span>
* <span data-ttu-id="859ee-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="859ee-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="859ee-308">**images**</span><span class="sxs-lookup"><span data-stu-id="859ee-308">**images**</span></span>
    * <span data-ttu-id="859ee-309">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="859ee-309">*banner1.svg*</span></span>
  * <span data-ttu-id="859ee-310">*default.html*</span><span class="sxs-lookup"><span data-stu-id="859ee-310">*default.html*</span></span>

<span data-ttu-id="859ee-311">次のコードにより、`MyStaticFiles` への静的ファイル、既定ファイル、ディレクトリ参照が有効になります。</span><span class="sxs-lookup"><span data-stu-id="859ee-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="859ee-312">`EnableDirectoryBrowsing` プロパティの値が `true` であるとき、`AddDirectoryBrowser` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="859ee-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="859ee-313">このファイル階層と前述のコードを使用すると、URL は次のように解決されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="859ee-314">URI</span><span class="sxs-lookup"><span data-stu-id="859ee-314">URI</span></span>            |                             <span data-ttu-id="859ee-315">応答</span><span class="sxs-lookup"><span data-stu-id="859ee-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="859ee-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="859ee-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="859ee-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="859ee-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="859ee-318">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="859ee-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="859ee-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="859ee-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="859ee-320">*MyStaticFiles* ディレクトリに既定の名前のファイルが存在しない場合、*http://\<server_address>/StaticFiles* によってクリック可能なリンクを含むディレクトリの一覧が返されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![静的ファイルの一覧](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="859ee-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> および <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> では、`http://{SERVER ADDRESS}/StaticFiles` (末尾のスラッシュなし) から `http://{SERVER ADDRESS}/StaticFiles/` (末尾のスラッシュあり) へのクライアント側リダイレクトが実行されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="859ee-323">*StaticFiles* ディレクトリ内の相対 URL は、末尾のスラッシュがないと無効です。</span><span class="sxs-lookup"><span data-stu-id="859ee-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="859ee-324">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="859ee-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="859ee-325"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> クラスには、MIME コンテンツ タイプへのファイル拡張子のマッピングを行う `Mappings` プロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="859ee-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="859ee-326">次の例では、いくつかのファイル拡張子は、既知の MIME タイプに登録されています。</span><span class="sxs-lookup"><span data-stu-id="859ee-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="859ee-327">*.rtf* 拡張子は置換され、 *.mp4* は削除されています。</span><span class="sxs-lookup"><span data-stu-id="859ee-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="859ee-328">「[MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml)」 (MIME コンテンツ タイプ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="859ee-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="859ee-329">カスタム <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> を使用する方法、または Blazor サーバー アプリで他の <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を構成する方法については、<xref:blazor/fundamentals/additional-scenarios#static-files> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="859ee-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="859ee-330">非標準のコンテンツ タイプ</span><span class="sxs-lookup"><span data-stu-id="859ee-330">Non-standard content types</span></span>

<span data-ttu-id="859ee-331">静的ファイル ミドルウェアでは、約 400 個の既知のファイル コンテンツ タイプが認識されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="859ee-332">ユーザーがファイルの種類が不明なファイルを要求した場合、静的ファイル ミドルウェアでその要求がパイプラインの次のミドルウェアに渡されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="859ee-333">ミドルウェアで要求が処理されない場合、*404 見つかりません* という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="859ee-334">ディレクトリ参照が有効になっている場合、ディレクトリ一覧にファイルへのリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="859ee-335">次のコードによって、不明なタイプを提供できるようにし、不明なファイルをイメージとしてレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="859ee-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="859ee-336">上記のコードでは、不明なコンテンツ タイプ ファイルに対する要求は、イメージとして返されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="859ee-337"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> を有効にすると、セキュリティ上リスクとなります。</span><span class="sxs-lookup"><span data-stu-id="859ee-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="859ee-338">これは既定では無効で、使用は推奨されていません。</span><span class="sxs-lookup"><span data-stu-id="859ee-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="859ee-339">非標準の拡張子のファイルを提供する場合、より安全な代替となるのは、[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) です。</span><span class="sxs-lookup"><span data-stu-id="859ee-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="859ee-340">複数の場所からファイルを提供する</span><span class="sxs-lookup"><span data-stu-id="859ee-340">Serve files from multiple locations</span></span>

<span data-ttu-id="859ee-341">`UseStaticFiles` と `UseFileServer` の既定では、*wwwroot* をポイントするファイル プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="859ee-342">他のファイル プロバイダーを使用する `UseStaticFiles` および `UseFileServer` の追加インスタンスを作成して、他の場所からファイルを提供することができます。</span><span class="sxs-lookup"><span data-stu-id="859ee-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="859ee-343">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/15578)します。</span><span class="sxs-lookup"><span data-stu-id="859ee-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="859ee-344">注意事項</span><span class="sxs-lookup"><span data-stu-id="859ee-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="859ee-345">`UseDirectoryBrowser` と `UseStaticFiles` では、機密データが漏洩することがあります。</span><span class="sxs-lookup"><span data-stu-id="859ee-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="859ee-346">本番では、ディレクトリ参照を無効にすることが、強く推奨されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="859ee-347">`UseStaticFiles` や `UseDirectoryBrowser` でどのディレクトリが有効になっているか、慎重にご確認ください。</span><span class="sxs-lookup"><span data-stu-id="859ee-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="859ee-348">ディレクトリ全体とそのサブディレクトリが、パブリックにアクセス可能になります。</span><span class="sxs-lookup"><span data-stu-id="859ee-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="859ee-349">ファイルは、パブリックに提供するのに適した、 *\<content_root>/wwwroot* などの専用ディレクトリに格納します。</span><span class="sxs-lookup"><span data-stu-id="859ee-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="859ee-350">これらのファイルは、MVC ビュー、Razor Pages (2.x のみ)、構成ファイルなどとは別にします。</span><span class="sxs-lookup"><span data-stu-id="859ee-350">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="859ee-351">`UseDirectoryBrowser` と `UseStaticFiles` で公開されるコンテンツの URL では、大文字と小文字が区別され、基になるファイル システムの文字制限の影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="859ee-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="859ee-352">たとえば、Windows では大文字小文字は区別されますが、macOS と Linux ではされません。</span><span class="sxs-lookup"><span data-stu-id="859ee-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="859ee-353">IIS でホストされている ASP.NET Core アプリは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を使用して、静的ファイルの要求を含む、すべての要求をアプリに転送します。</span><span class="sxs-lookup"><span data-stu-id="859ee-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="859ee-354">IIS 静的ファイル ハンドラーは使用されません。</span><span class="sxs-lookup"><span data-stu-id="859ee-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="859ee-355">モジュールによって処理される前に、これで要求が処理されることはありません。</span><span class="sxs-lookup"><span data-stu-id="859ee-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="859ee-356">IIS マネージャーで次の手順を実行し、サーバーまたは Web サイト レベルで IIS の静的ファイル ハンドラーを削除します。</span><span class="sxs-lookup"><span data-stu-id="859ee-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="859ee-357">**[モジュール]** 機能に移動します。</span><span class="sxs-lookup"><span data-stu-id="859ee-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="859ee-358">一覧の **[StaticFileModule]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="859ee-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="859ee-359">**[アクション]** サイドバーで、 **[削除]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="859ee-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="859ee-360">IIS の静的ファイル ハンドラーが有効になっており、**かつ**、ASP.NET Core モジュールが正しく構成されていない場合、静的ファイルにサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="859ee-361">これは、たとえば、*web.config* ファイルが配置されていない場合などで発生します。</span><span class="sxs-lookup"><span data-stu-id="859ee-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="859ee-362">アプリ プロジェクトの [Web ルート](xref:fundamentals/index#web-root)の外に、コード ファイル ( *.cs* と *.cshtml* を含む) を配置します。</span><span class="sxs-lookup"><span data-stu-id="859ee-362">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="859ee-363">これにより、アプリのクライアント側コンテンツとサーバー ベースのコードの間で、論理的な分離が作成されます。</span><span class="sxs-lookup"><span data-stu-id="859ee-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="859ee-364">これによって、サーバー側のコードが漏洩するのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="859ee-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="859ee-365">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="859ee-365">Additional resources</span></span>

* [<span data-ttu-id="859ee-366">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="859ee-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="859ee-367">ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="859ee-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
