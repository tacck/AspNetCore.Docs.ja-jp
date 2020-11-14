---
title: ASP.NET Core のエラーを処理する
author: rick-anderson
description: ASP.NET Core アプリでエラーを処理する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: c8174c7e253a596d02dbc6cec183453b3723bc24
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060470"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="cc827-103">ASP.NET Core のエラーを処理する</span><span class="sxs-lookup"><span data-stu-id="cc827-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cc827-104">作成者: [Kirk Larkin](https://twitter.com/serpent5)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cc827-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cc827-105">この記事では、ASP.NET Core Web アプリでエラーを処理するための一般的な手法について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="cc827-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="cc827-106">Web API については、<xref:web-api/handle-errors> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc827-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="cc827-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)します。</span><span class="sxs-lookup"><span data-stu-id="cc827-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="cc827-108">([ダウンロード方法](xref:index#how-to-download-a-sample)。)F12 ブラウザー開発者ツールの [ネットワーク] タブは、サンプル アプリをテストする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="cc827-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="cc827-109">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="cc827-109">Developer Exception Page</span></span>

<span data-ttu-id="cc827-110">" *開発者例外ページ* " には、要求の例外に関する詳細な情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="cc827-111">ASP.NET Core テンプレートにより、次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="cc827-112">前の強調表示されたコードは、アプリが[開発環境](xref:fundamentals/environments)で実行されている場合に、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="cc827-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="cc827-113">このテンプレートでは、後続のミドルウェアでスローされた例外をキャッチできるように、ミドルウェア パイプラインの早い段階で <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> が配置されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="cc827-114">前のコードでは、アプリが開発環境で実行されている場合に "\* **のみ** _"、開発者例外ページが有効になります。</span><span class="sxs-lookup"><span data-stu-id="cc827-114">The preceding code enables the Developer Exception Page \* **only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="cc827-115">アプリが運用環境で実行されている場合は、詳細な例外情報を公開しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="cc827-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="cc827-116">環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cc827-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cc827-117">開発者例外ページには、例外と要求に関する次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc827-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="cc827-118">_ スタック トレース</span><span class="sxs-lookup"><span data-stu-id="cc827-118">_ Stack trace</span></span>
* <span data-ttu-id="cc827-119">クエリ文字列のパラメーター (ある場合)</span><span class="sxs-lookup"><span data-stu-id="cc827-119">Query string parameters if any</span></span>
* <span data-ttu-id="cc827-120">Cookie (ある場合)</span><span class="sxs-lookup"><span data-stu-id="cc827-120">Cookies if any</span></span>
* <span data-ttu-id="cc827-121">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="cc827-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="cc827-122">例外ハンドラー ページ</span><span class="sxs-lookup"><span data-stu-id="cc827-122">Exception handler page</span></span>

<span data-ttu-id="cc827-123">[運用環境](xref:fundamentals/environments)のカスタム エラー処理ページを構成するには、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cc827-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="cc827-124">この例外処理ミドルウェアは、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="cc827-124">This exception handling middleware:</span></span>

* <span data-ttu-id="cc827-125">例外をキャッチしてログに記録します。</span><span class="sxs-lookup"><span data-stu-id="cc827-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="cc827-126">指定されたパスを使用して、別のパイプラインで要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="cc827-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="cc827-127">応答が始まっていた場合、要求は再実行されません。</span><span class="sxs-lookup"><span data-stu-id="cc827-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="cc827-128">テンプレートによって生成されたコードは、`/Error` パスを使用して要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="cc827-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="cc827-129">次の例では、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> により非開発環境に例外処理ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="cc827-130">Razor Pages アプリのテンプレートには、エラー ページ ( *.cshtml* ) と <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> クラス (`ErrorModel`) が *Pages* フォルダー内に用意されています。</span><span class="sxs-lookup"><span data-stu-id="cc827-130">The Razor Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="cc827-131">MVC アプリの場合、プロジェクト テンプレートには、Home コントローラーの `Error` アクション メソッドとエラー ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc827-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="cc827-132">`HttpGet` などの HTTP メソッド属性を使ってエラー ハンドラー アクション メソッドをマークしないでください。</span><span class="sxs-lookup"><span data-stu-id="cc827-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="cc827-133">明示的な動詞を使用すると、要求がアクション メソッドに届かないことがあります。</span><span class="sxs-lookup"><span data-stu-id="cc827-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="cc827-134">認証されていないユーザーにエラー ビューが表示される場合は、メソッドへの匿名アクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="cc827-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="cc827-135">例外にアクセスする</span><span class="sxs-lookup"><span data-stu-id="cc827-135">Access the exception</span></span>

<span data-ttu-id="cc827-136">エラー ハンドラーで例外や元の要求パスにアクセスするには、<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="cc827-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="cc827-137">次のコードは、ASP.NET Core テンプレートによって生成される既定の *Pages/Error.cshtml.cs* に `ExceptionMessage` を追加します。</span><span class="sxs-lookup"><span data-stu-id="cc827-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="cc827-138">機密性の高いエラー情報をクライアントに提供 **しないでください** 。</span><span class="sxs-lookup"><span data-stu-id="cc827-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="cc827-139">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="cc827-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="cc827-140">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で例外をテストするには:</span><span class="sxs-lookup"><span data-stu-id="cc827-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="cc827-141">環境を運用環境に設定します。</span><span class="sxs-lookup"><span data-stu-id="cc827-141">Set the environment to production.</span></span>
* <span data-ttu-id="cc827-142">*Program.cs* の `webBuilder.UseStartup<Startup>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="cc827-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="cc827-143">ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc827-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="cc827-144">例外ハンドラー ラムダ</span><span class="sxs-lookup"><span data-stu-id="cc827-144">Exception handler lambda</span></span>

<span data-ttu-id="cc827-145">[カスタム例外ハンドラー ページ](#exception-handler-page)の代わりになるのは、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> にラムダを提供することです。</span><span class="sxs-lookup"><span data-stu-id="cc827-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="cc827-146">ラムダを使うと、応答を返す前にエラーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="cc827-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="cc827-147">次のコードは、例外処理にラムダを使用しています。</span><span class="sxs-lookup"><span data-stu-id="cc827-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="cc827-148"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> または <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> からの機密性の高いエラー情報をクライアントに提供 **しないでください** 。</span><span class="sxs-lookup"><span data-stu-id="cc827-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="cc827-149">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="cc827-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="cc827-150">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で例外処理ラムダをテストするには:</span><span class="sxs-lookup"><span data-stu-id="cc827-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="cc827-151">環境を運用環境に設定します。</span><span class="sxs-lookup"><span data-stu-id="cc827-151">Set the environment to production.</span></span>
* <span data-ttu-id="cc827-152">*Program.cs* の `webBuilder.UseStartup<StartupLambda>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="cc827-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="cc827-153">ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc827-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="cc827-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="cc827-154">UseStatusCodePages</span></span>

<span data-ttu-id="cc827-155">ASP.NET Core アプリでは、既定で、" *404 - 見つかりません* " などの HTTP エラー状態コードの状態コード ページが表示されません。</span><span class="sxs-lookup"><span data-stu-id="cc827-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="cc827-156">アプリで、本文のない HTTP 400 - 499 のエラー条件が発生すると、状態コードと空の応答本文が返されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-156">When the app encounters an HTTP 400-499 error condition that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="cc827-157">状態コード ページを提供するには、状態コード ページ ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc827-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="cc827-158">一般的なエラー状態コード用に既定のテキスト専用ハンドラーを有効にするには、`Startup.Configure` メソッドで <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cc827-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

<span data-ttu-id="cc827-159">要求処理ミドルウェアの前に `UseStatusCodePages` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cc827-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="cc827-160">たとえば、静的ファイル ミドルウェアとエンドポイント ミドルウェアの前に `UseStatusCodePages` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cc827-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="cc827-161">`UseStatusCodePages` を使用しない場合、エンドポイントなしで URL に移動すると、エンドポイントが見つからないことを示すブラウザー依存のエラー メッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="cc827-162">たとえば、`Home/Privacy2` に移動します。</span><span class="sxs-lookup"><span data-stu-id="cc827-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="cc827-163">`UseStatusCodePages` が呼び出されると、ブラウザーにより次が返されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="cc827-164">`UseStatusCodePages` は、ユーザーにとって役に立たないメッセージを返すため、通常は、運用環境では使用されません。</span><span class="sxs-lookup"><span data-stu-id="cc827-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="cc827-165">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには:</span><span class="sxs-lookup"><span data-stu-id="cc827-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="cc827-166">環境を運用環境に設定します。</span><span class="sxs-lookup"><span data-stu-id="cc827-166">Set the environment to production.</span></span>
* <span data-ttu-id="cc827-167">*Program.cs* の `webBuilder.UseStartup<StartupUseStatusCodePages>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="cc827-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="cc827-168">ホーム ページにあるリンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc827-168">Select the links on the home page on the home page.</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="cc827-169">書式設定文字列での UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="cc827-169">UseStatusCodePages with format string</span></span>

<span data-ttu-id="cc827-170">応答の内容の種類とテキストをカスタマイズするには、内容の種類と書式文字列を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="cc827-170">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="cc827-171">上記のコードでは、`{0}` はエラー コードのプレースホルダーです。</span><span class="sxs-lookup"><span data-stu-id="cc827-171">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="cc827-172">書式指定文字列を含む `UseStatusCodePages` は、ユーザーにとって役に立たないメッセージを返すため、通常は、運用環境では使用されません。</span><span class="sxs-lookup"><span data-stu-id="cc827-172">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="cc827-173">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには、 *Program.cs* の `webBuilder.UseStartup<StartupFormat>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="cc827-173">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="cc827-174">ラムダでの UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="cc827-174">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="cc827-175">カスタム エラー処理と応答書き込みコードを指定するには、ラムダ式を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="cc827-175">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="cc827-176">ラムダを含む `UseStatusCodePages` は、ユーザーにとって役に立たないメッセージを返すため、通常は、運用環境では使用されません。</span><span class="sxs-lookup"><span data-stu-id="cc827-176">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="cc827-177">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには、 *Program.cs* の `webBuilder.UseStartup<StartupStatusLambda>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="cc827-177">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="cc827-178">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="cc827-178">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="cc827-179"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="cc827-179">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="cc827-180">クライアントに [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) 状態コードを送信します。</span><span class="sxs-lookup"><span data-stu-id="cc827-180">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="cc827-181">URL テンプレートで指定されているエラー処理エンドポイントにクライアントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="cc827-181">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="cc827-182">エラー処理エンドポイントには、通常、エラー情報が表示され、HTTP 200 が返されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-182">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="cc827-183">前のコードに示されているように、URL テンプレートには状態コード用の `{0}` プレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="cc827-183">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="cc827-184">URL テンプレートが `~` (チルダ) で始まっている場合、`~` はアプリの `PathBase` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cc827-184">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="cc827-185">アプリでエンドポイントを指定するときに、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="cc827-185">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="cc827-186">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)にある [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cc827-186">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="cc827-187">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-187">This method is commonly used when the app:</span></span>

* <span data-ttu-id="cc827-188">クライアントを別のエンドポイントにリダイレクトする必要がある場合 (通常は、別のアプリがエラーを処理する場合)。</span><span class="sxs-lookup"><span data-stu-id="cc827-188">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="cc827-189">Web アプリの場合は、クライアントのブラウザーのアドレス バーにリダイレクトされたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-189">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="cc827-190">元の状態コードを保持し、初回のリダイレクト応答で返してはいけない場合。</span><span class="sxs-lookup"><span data-stu-id="cc827-190">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="cc827-191">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには、 *Program.cs* の `webBuilder.UseStartup<StartupSCredirect>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="cc827-191">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="cc827-192">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="cc827-192">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="cc827-193"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="cc827-193">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="cc827-194">元の状態コードをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="cc827-194">Returns the original status code to the client.</span></span>
* <span data-ttu-id="cc827-195">代替パスを使用して要求パイプラインを再実行することで、応答本文を生成します。</span><span class="sxs-lookup"><span data-stu-id="cc827-195">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="cc827-196">アプリ内でエンドポイントが指定されている場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="cc827-196">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="cc827-197">`UseRouting` の前に `UseStatusCodePagesWithReExecute` が配置されていることを確認して、要求を状態ページに再ルーティングできるようにします。</span><span class="sxs-lookup"><span data-stu-id="cc827-197">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="cc827-198">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)にある [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cc827-198">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="cc827-199">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-199">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="cc827-200">別のエンドポイントにリダイレクトすることなく要求を処理する。</span><span class="sxs-lookup"><span data-stu-id="cc827-200">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="cc827-201">Web アプリの場合は、クライアントのブラウザーのアドレス バーに、初めに要求されていたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-201">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="cc827-202">元の状態コードを保持し、応答で返す。</span><span class="sxs-lookup"><span data-stu-id="cc827-202">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="cc827-203">URL とクエリ文字列のテンプレートには、状態コード用のプレースホルダー `{0}` を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="cc827-203">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="cc827-204">URL テンプレートの先頭には、`/` を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc827-204">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="cc827-205">次の例で示すように、エラーを処理するエンドポイントでは、エラーを生成した元の URL を取得できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-205">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="cc827-206">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)にある [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cc827-206">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="cc827-207">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには、 *Program.cs* の `webBuilder.UseStartup<StartupSCreX>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="cc827-207">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="cc827-208">状態コード ページを無効にする</span><span class="sxs-lookup"><span data-stu-id="cc827-208">Disable status code pages</span></span>

<span data-ttu-id="cc827-209">MVC コントローラーまたはアクション メソッドの状態コード ページを無効にするには、[[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc827-209">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="cc827-210">Razor Pages ハンドラー メソッドまたは MVC コントローラーの特定の要求に対して状態コード ページを無効にするには、<xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc827-210">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="cc827-211">例外処理コード</span><span class="sxs-lookup"><span data-stu-id="cc827-211">Exception-handling code</span></span>

<span data-ttu-id="cc827-212">例外処理ページのコードが例外をスローすることもあります。</span><span class="sxs-lookup"><span data-stu-id="cc827-212">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="cc827-213">運用環境のエラー ページは十分にテストし、それ自体から例外がスローされないように特に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc827-213">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="cc827-214">応答ヘッダー</span><span class="sxs-lookup"><span data-stu-id="cc827-214">Response headers</span></span>

<span data-ttu-id="cc827-215">応答のヘッダーが送信された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cc827-215">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="cc827-216">アプリで応答の状態コードを変更できません。</span><span class="sxs-lookup"><span data-stu-id="cc827-216">The app can't change the response's status code.</span></span>
* <span data-ttu-id="cc827-217">すべての例外ページやハンドラーを実行できません。</span><span class="sxs-lookup"><span data-stu-id="cc827-217">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="cc827-218">応答は完了している必要があります。あるいは、接続が中止となっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc827-218">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="cc827-219">サーバー例外処理</span><span class="sxs-lookup"><span data-stu-id="cc827-219">Server exception handling</span></span>

<span data-ttu-id="cc827-220">アプリ内の例外処理ロジックに加えて、[HTTP サーバーの実装](xref:fundamentals/servers/index)でも一部の例外を処理できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-220">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="cc827-221">応答ヘッダーの送信前にサーバーで例外がキャッチされると、サーバーによって "`500 - Internal Server Error`" 応答が応答本文なしで送信されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-221">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="cc827-222">応答ヘッダーの送信後にサーバーで例外がキャッチされた場合、サーバーは接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="cc827-222">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="cc827-223">アプリで処理されない要求はサーバーで処理されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-223">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="cc827-224">サーバーが要求を処理しているときに発生した例外は、すべてサーバーの例外処理によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-224">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="cc827-225">この動作は、アプリのカスタム エラー ページ、例外処理ミドルウェア、およびフィルターから影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="cc827-225">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="cc827-226">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="cc827-226">Startup exception handling</span></span>

<span data-ttu-id="cc827-227">アプリの起動中に起こる例外はホスティング層だけが処理できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-227">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="cc827-228">[起動時のエラーをキャプチャ](xref:fundamentals/host/web-host#capture-startup-errors)したり、[詳細なエラーをキャプチャ](xref:fundamentals/host/web-host#detailed-errors)したりするように、ホストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="cc827-228">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="cc827-229">ホスティング レイヤーでは、ホスト アドレス/ポート バインド後にエラーが発生した場合にのみ、キャプチャされた起動時エラーに対するエラー ページを表示できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-229">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="cc827-230">バインドが失敗した場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cc827-230">If binding fails:</span></span>

* <span data-ttu-id="cc827-231">ホスティング レイヤーにより重大な例外がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-231">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="cc827-232">dotnet プロセスがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="cc827-232">The dotnet process crashes.</span></span>
* <span data-ttu-id="cc827-233">HTTP サーバーが [Kestrel](xref:fundamentals/servers/kestrel) のときは、エラー ページは表示されません。</span><span class="sxs-lookup"><span data-stu-id="cc827-233">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="cc827-234">[IIS](/iis) (または Azure App Service) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上で実行している場合、プロセスを開始できなければ、 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)から " *502.5 - 処理エラー* " が返されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-234">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="cc827-235">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc827-235">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="cc827-236">データベース エラー ページ</span><span class="sxs-lookup"><span data-stu-id="cc827-236">Database error page</span></span>

<span data-ttu-id="cc827-237">データベース開発者ページ例外フィルター `AddDatabaseDeveloperPageExceptionFilter` では、Entity Framework Core の移行を使って解決できるデータベース関連の例外がキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="cc827-237">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="cc827-238">これらの例外が発生すると、問題が解決する可能性のあるアクションの詳細を含む HTML 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-238">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="cc827-239">このページは、開発環境でのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="cc827-239">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="cc827-240">次のコードは、個々のユーザー アカウントが指定されたときに、ASP.NET Core Razor Pages テンプレートによって生成されたものです。</span><span class="sxs-lookup"><span data-stu-id="cc827-240">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="cc827-241">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="cc827-241">Exception filters</span></span>

<span data-ttu-id="cc827-242">MVC アプリでは、例外フィルターをグローバルに、またはコントローラーやアクションの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-242">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="cc827-243">Razor Pages アプリでは、グローバルに、またはページ モデルの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-243">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="cc827-244">このようなフィルターは、コントローラー アクションや別のフィルターの実行中に発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="cc827-244">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="cc827-245">詳細については、「<xref:mvc/controllers/filters#exception-filters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc827-245">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="cc827-246">例外フィルターは、MVC アクション内で発生する例外をトラップする場合には便利ですが、組み込みの[例外処理ミドルウェア](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` ほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="cc827-246">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="cc827-247">選択された MVC アクションに応じて異なる方法でエラー処理を実行する必要がある場合を除き、`UseExceptionHandler` を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc827-247">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="cc827-248">モデル状態エラー</span><span class="sxs-lookup"><span data-stu-id="cc827-248">Model state errors</span></span>

<span data-ttu-id="cc827-249">モデル状態エラーを処理する方法については、[モデル バインド](xref:mvc/models/model-binding)および[モデルの検証](xref:mvc/models/validation)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cc827-249">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc827-250">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cc827-250">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cc827-251">作成者: [Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cc827-251">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cc827-252">この記事では、ASP.NET Core Web アプリでエラーを処理するための一般的な手法について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="cc827-252">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="cc827-253">Web API については、<xref:web-api/handle-errors> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc827-253">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="cc827-254">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)します。</span><span class="sxs-lookup"><span data-stu-id="cc827-254">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="cc827-255">([ダウンロード方法](xref:index#how-to-download-a-sample)。)</span><span class="sxs-lookup"><span data-stu-id="cc827-255">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="cc827-256">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="cc827-256">Developer Exception Page</span></span>

<span data-ttu-id="cc827-257">" *開発者例外ページ* " には、要求の例外に関する詳細な情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-257">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="cc827-258">ASP.NET Core テンプレートにより、次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-258">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="cc827-259">前のコードは、アプリが[開発環境](xref:fundamentals/environments)で実行されている場合に、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="cc827-259">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="cc827-260">このテンプレートでは、後続のミドルウェアで例外をキャッチできるように、ミドルウェアの前に <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> が配置されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-260">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="cc827-261">前のコードは、アプリが **開発環境** で実行されている場合にのみ、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="cc827-261">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="cc827-262">アプリが運用環境で実行されている場合は、詳細な例外情報を公開しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="cc827-262">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="cc827-263">環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cc827-263">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cc827-264">開発者例外ページには、例外と要求に関する次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc827-264">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="cc827-265">スタック トレース</span><span class="sxs-lookup"><span data-stu-id="cc827-265">Stack trace</span></span>
* <span data-ttu-id="cc827-266">クエリ文字列のパラメーター (ある場合)</span><span class="sxs-lookup"><span data-stu-id="cc827-266">Query string parameters if any</span></span>
* <span data-ttu-id="cc827-267">Cookie (ある場合)</span><span class="sxs-lookup"><span data-stu-id="cc827-267">Cookies if any</span></span>
* <span data-ttu-id="cc827-268">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="cc827-268">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="cc827-269">例外ハンドラー ページ</span><span class="sxs-lookup"><span data-stu-id="cc827-269">Exception handler page</span></span>

<span data-ttu-id="cc827-270">運用環境用のカスタム エラー処理ページを構成するには、例外処理ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc827-270">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="cc827-271">ミドルウェアでは次を行います。</span><span class="sxs-lookup"><span data-stu-id="cc827-271">The middleware:</span></span>

* <span data-ttu-id="cc827-272">例外をキャッチしてログに記録します。</span><span class="sxs-lookup"><span data-stu-id="cc827-272">Catches and logs exceptions.</span></span>
* <span data-ttu-id="cc827-273">ページ用の、またはコントローラーが指定した別のパイプラインで、要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="cc827-273">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="cc827-274">応答が始まっていた場合、要求は再実行されません。</span><span class="sxs-lookup"><span data-stu-id="cc827-274">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="cc827-275">テンプレートによって生成されたコードは、`/Error` への要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="cc827-275">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="cc827-276">次の例では、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> により非開発環境に例外処理ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-276">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="cc827-277">Razor Pages アプリのテンプレートには、エラー ページ ( *.cshtml* ) と <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> クラス (`ErrorModel`) が *Pages* フォルダー内に用意されています。</span><span class="sxs-lookup"><span data-stu-id="cc827-277">The Razor Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="cc827-278">MVC アプリの場合、プロジェクト テンプレートには、Home コントローラーのエラー アクション メソッドとエラー ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc827-278">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="cc827-279">`HttpGet` などの HTTP メソッド属性を使ってエラー ハンドラー アクション メソッドをマークしないでください。</span><span class="sxs-lookup"><span data-stu-id="cc827-279">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="cc827-280">明示的な動詞を使用すると、要求がメソッドに届かないことがあります。</span><span class="sxs-lookup"><span data-stu-id="cc827-280">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="cc827-281">認証されていないユーザーにエラー ビューが表示される場合は、メソッドへの匿名アクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="cc827-281">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="cc827-282">例外にアクセスする</span><span class="sxs-lookup"><span data-stu-id="cc827-282">Access the exception</span></span>

<span data-ttu-id="cc827-283">エラー ハンドラー コントローラーまたはページ内で例外や元の要求パスにアクセスするには、<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="cc827-283">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="cc827-284">機密性の高いエラー情報をクライアントに提供 **しないでください** 。</span><span class="sxs-lookup"><span data-stu-id="cc827-284">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="cc827-285">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="cc827-285">Serving errors is a security risk.</span></span>

<span data-ttu-id="cc827-286">上記の例外処理ページをトリガーするには、環境を運用に設定し、例外を強制します。</span><span class="sxs-lookup"><span data-stu-id="cc827-286">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="cc827-287">例外ハンドラー ラムダ</span><span class="sxs-lookup"><span data-stu-id="cc827-287">Exception handler lambda</span></span>

<span data-ttu-id="cc827-288">[カスタム例外ハンドラー ページ](#exception-handler-page)の代わりになるのは、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> にラムダを提供することです。</span><span class="sxs-lookup"><span data-stu-id="cc827-288">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="cc827-289">ラムダを使うと、応答を返す前にエラーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="cc827-289">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="cc827-290">例外処理にラムダを使う例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cc827-290">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="cc827-291">上記のコードでは `await context.Response.WriteAsync(new string(' ', 512));` が追加されるため、Internet Explorer ブラウザーには IE のエラー メッセージではなく、このエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-291">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="cc827-292">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/16144)します。</span><span class="sxs-lookup"><span data-stu-id="cc827-292">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="cc827-293"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> または <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> からの機密性の高いエラー情報をクライアントに提供 **しないでください** 。</span><span class="sxs-lookup"><span data-stu-id="cc827-293">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="cc827-294">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="cc827-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="cc827-295">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で例外処理ラムダの結果を表示するには、`ProdEnvironment` および `ErrorHandlerLambda` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc827-295">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="cc827-296">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="cc827-296">UseStatusCodePages</span></span>

<span data-ttu-id="cc827-297">ASP.NET Core アプリでは、既定で、" *404 - 見つかりません* " などの HTTP 状態コードの状態コード ページが表示されません。</span><span class="sxs-lookup"><span data-stu-id="cc827-297">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="cc827-298">アプリでは、状態コードと空の応答本文が返されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-298">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="cc827-299">状態コード ページを提供するには、状態コード ページ ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc827-299">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="cc827-300">そのミドルウェアは、[Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="cc827-300">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="cc827-301">一般的なエラー状態コード用に既定のテキスト専用ハンドラーを有効にするには、`Startup.Configure` メソッドで <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cc827-301">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="cc827-302">要求処理ミドルウェア (たとえば、静的ファイル ミドルウェアや MVC ミドルウェア) の前に `UseStatusCodePages` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cc827-302">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="cc827-303">`UseStatusCodePages` を使用しない場合、エンドポイントなしで URL に移動すると、エンドポイントが見つからないことを示すブラウザー依存のエラー メッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-303">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="cc827-304">たとえば、`Home/Privacy2` に移動します。</span><span class="sxs-lookup"><span data-stu-id="cc827-304">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="cc827-305">`UseStatusCodePages` が呼び出されると、ブラウザーにより次が返されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-305">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="cc827-306">書式設定文字列での UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="cc827-306">UseStatusCodePages with format string</span></span>

<span data-ttu-id="cc827-307">応答の内容の種類とテキストをカスタマイズするには、内容の種類と書式文字列を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="cc827-307">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="cc827-308">ラムダでの UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="cc827-308">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="cc827-309">カスタム エラー処理と応答書き込みコードを指定するには、ラムダ式を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="cc827-309">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="cc827-310">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="cc827-310">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="cc827-311"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="cc827-311">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="cc827-312">クライアントに *302 - Found* 状態コードを送信します。</span><span class="sxs-lookup"><span data-stu-id="cc827-312">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="cc827-313">URL テンプレートで指定された場所にクライアントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="cc827-313">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="cc827-314">次の例で示すように、URL テンプレートには状態コード用の `{0}` プレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="cc827-314">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="cc827-315">URL テンプレートが `~` (チルダ) で始まっている場合、`~` はアプリの `PathBase` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cc827-315">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="cc827-316">アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="cc827-316">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="cc827-317">Razor Pages の例については、 [サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cc827-317">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="cc827-318">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-318">This method is commonly used when the app:</span></span>

* <span data-ttu-id="cc827-319">クライアントを別のエンドポイントにリダイレクトする必要がある場合 (通常は、別のアプリがエラーを処理する場合)。</span><span class="sxs-lookup"><span data-stu-id="cc827-319">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="cc827-320">Web アプリの場合は、クライアントのブラウザーのアドレス バーにリダイレクトされたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-320">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="cc827-321">元の状態コードを保持し、初回のリダイレクト応答で返してはいけない場合。</span><span class="sxs-lookup"><span data-stu-id="cc827-321">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="cc827-322">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="cc827-322">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="cc827-323"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="cc827-323">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="cc827-324">元の状態コードをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="cc827-324">Returns the original status code to the client.</span></span>
* <span data-ttu-id="cc827-325">代替パスを使用して要求パイプラインを再実行することで、応答本文を生成します。</span><span class="sxs-lookup"><span data-stu-id="cc827-325">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="cc827-326">アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="cc827-326">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="cc827-327">`UseRouting` の前に `UseStatusCodePagesWithReExecute` が配置されていることを確認して、要求を状態ページに再ルーティングできるようにします。</span><span class="sxs-lookup"><span data-stu-id="cc827-327">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="cc827-328">Razor Pages の例については、 [サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cc827-328">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="cc827-329">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-329">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="cc827-330">別のエンドポイントにリダイレクトすることなく要求を処理する。</span><span class="sxs-lookup"><span data-stu-id="cc827-330">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="cc827-331">Web アプリの場合は、クライアントのブラウザーのアドレス バーに、初めに要求されていたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-331">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="cc827-332">元の状態コードを保持し、応答で返す。</span><span class="sxs-lookup"><span data-stu-id="cc827-332">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="cc827-333">URL とクエリ文字列のテンプレートには、状態コード用のプレースホルダー (`{0}`) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="cc827-333">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="cc827-334">URL テンプレートの先頭には、スラッシュ (`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc827-334">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="cc827-335">パスでプレースホルダーを使う場合は、エンドポイント (ページまたはコントローラー) でパスのセグメントを処理できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="cc827-335">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="cc827-336">たとえば、エラー用の Razor ページでは、`@page` ディレクティブの付いた省略可能なパスのセグメント値を受け入れる必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc827-336">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="cc827-337">次の例で示すように、エラーを処理するエンドポイントでは、エラーを生成した元の URL を取得できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-337">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="cc827-338">状態コード ページを無効にする</span><span class="sxs-lookup"><span data-stu-id="cc827-338">Disable status code pages</span></span>

<span data-ttu-id="cc827-339">MVC コントローラーまたはアクション メソッドの状態コード ページを無効にするには、[`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc827-339">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="cc827-340">Razor Pages ハンドラー メソッドまたは MVC コントローラーの特定の要求に対して状態コード ページを無効にするには、<xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc827-340">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="cc827-341">例外処理コード</span><span class="sxs-lookup"><span data-stu-id="cc827-341">Exception-handling code</span></span>

<span data-ttu-id="cc827-342">例外処理ページのコードは例外をスローすることがあります。</span><span class="sxs-lookup"><span data-stu-id="cc827-342">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="cc827-343">実稼働のエラー ページは純粋に静的なコンテンツで構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc827-343">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="cc827-344">応答ヘッダー</span><span class="sxs-lookup"><span data-stu-id="cc827-344">Response headers</span></span>

<span data-ttu-id="cc827-345">応答のヘッダーが送信された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cc827-345">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="cc827-346">アプリで応答の状態コードを変更できません。</span><span class="sxs-lookup"><span data-stu-id="cc827-346">The app can't change the response's status code.</span></span>
* <span data-ttu-id="cc827-347">すべての例外ページやハンドラーを実行できません。</span><span class="sxs-lookup"><span data-stu-id="cc827-347">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="cc827-348">応答は完了している必要があります。あるいは、接続が中止となっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc827-348">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="cc827-349">サーバー例外処理</span><span class="sxs-lookup"><span data-stu-id="cc827-349">Server exception handling</span></span>

<span data-ttu-id="cc827-350">アプリ内の例外処理ロジックに加えて、[HTTP サーバーの実装](xref:fundamentals/servers/index)でも一部の例外を処理できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-350">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="cc827-351">応答ヘッダーの送信前にサーバーで例外がキャッチされると、サーバーによって " *500 - 内部サーバー エラーです* " 応答が応答本文なしで送信されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-351">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="cc827-352">応答ヘッダーの送信後にサーバーで例外がキャッチされた場合、サーバーは接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="cc827-352">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="cc827-353">アプリで処理されない要求はサーバーで処理されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-353">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="cc827-354">サーバーが要求を処理しているときに発生した例外は、すべてサーバーの例外処理によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-354">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="cc827-355">この動作は、アプリのカスタム エラー ページ、例外処理ミドルウェア、およびフィルターから影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="cc827-355">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="cc827-356">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="cc827-356">Startup exception handling</span></span>

<span data-ttu-id="cc827-357">アプリの起動中に起こる例外はホスティング層だけが処理できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-357">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="cc827-358">[起動時のエラーをキャプチャ](xref:fundamentals/host/web-host#capture-startup-errors)したり、[詳細なエラーをキャプチャ](xref:fundamentals/host/web-host#detailed-errors)したりするように、ホストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="cc827-358">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="cc827-359">ホスティング レイヤーでは、ホスト アドレス/ポート バインド後にエラーが発生した場合にのみ、キャプチャされた起動時エラーに対するエラー ページを表示できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-359">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="cc827-360">バインドが失敗した場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cc827-360">If binding fails:</span></span>

* <span data-ttu-id="cc827-361">ホスティング レイヤーにより重大な例外がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-361">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="cc827-362">dotnet プロセスがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="cc827-362">The dotnet process crashes.</span></span>
* <span data-ttu-id="cc827-363">HTTP サーバーが [Kestrel](xref:fundamentals/servers/kestrel) のときは、エラー ページは表示されません。</span><span class="sxs-lookup"><span data-stu-id="cc827-363">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="cc827-364">[IIS](/iis) (または Azure App Service) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上で実行している場合、プロセスを開始できなければ、 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)から " *502.5 - 処理エラー* " が返されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-364">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="cc827-365">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc827-365">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="cc827-366">データベース エラー ページ</span><span class="sxs-lookup"><span data-stu-id="cc827-366">Database error page</span></span>

<span data-ttu-id="cc827-367">データベース エラー ページ ミドルウェアでは、Entity Framework の移行を使って解決できるデータベース関連の例外がキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="cc827-367">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="cc827-368">これらの例外が発生すると、問題が解決する可能性のあるアクションの詳細を含む HTML 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cc827-368">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="cc827-369">このページは、開発環境でのみ有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc827-369">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="cc827-370">ページを有効にするには、コードを `Startup.Configure` に追加します。</span><span class="sxs-lookup"><span data-stu-id="cc827-370">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="cc827-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> には、[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="cc827-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="cc827-372">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="cc827-372">Exception filters</span></span>

<span data-ttu-id="cc827-373">MVC アプリでは、例外フィルターをグローバルに、またはコントローラーやアクションの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-373">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="cc827-374">Razor Pages アプリでは、グローバルに、またはページ モデルの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="cc827-374">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="cc827-375">このようなフィルターはコントローラー アクションや別のフィルターの実行中に発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="cc827-375">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="cc827-376">詳細については、「<xref:mvc/controllers/filters#exception-filters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc827-376">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="cc827-377">例外フィルターは、MVC アクション内で発生した例外をトラップする場合は便利ですが、例外処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="cc827-377">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="cc827-378">ミドルウェアの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc827-378">We recommend using the middleware.</span></span> <span data-ttu-id="cc827-379">選択された MVC アクションに応じて異なる方法でエラー処理を実行する必要がある場合にのみ、フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc827-379">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="cc827-380">モデル状態エラー</span><span class="sxs-lookup"><span data-stu-id="cc827-380">Model state errors</span></span>

<span data-ttu-id="cc827-381">モデル状態エラーを処理する方法については、[モデル バインド](xref:mvc/models/model-binding)および[モデルの検証](xref:mvc/models/validation)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cc827-381">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc827-382">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cc827-382">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
