---
title: ASP.NET Core のミドルウェア
author: rick-anderson
description: ASP.NET Core のミドルウェアと要求パイプラインについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/middleware/index
ms.openlocfilehash: 06f55647ba2bb41152e16bb054e098abbe157cb8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059365"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="9d904-103">ASP.NET Core のミドルウェア</span><span class="sxs-lookup"><span data-stu-id="9d904-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9d904-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9d904-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9d904-105">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="9d904-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="9d904-106">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d904-106">Each component:</span></span>

* <span data-ttu-id="9d904-107">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d904-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="9d904-108">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="9d904-109">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="9d904-110">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="9d904-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="9d904-111">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="9d904-112">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="9d904-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="9d904-113">これらの再利用可能なクラスとインラインの匿名メソッドは、" *ミドルウェア* " です。" *ミドルウェア コンポーネント* " とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9d904-113">These reusable classes and in-line anonymous methods are *middleware* , also called *middleware components* .</span></span> <span data-ttu-id="9d904-114">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="9d904-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="9d904-115">ショートサーキットしたミドルウェアは " *ターミナル ミドルウェア* " と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="9d904-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="9d904-116"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="9d904-117">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="9d904-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="9d904-118">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="9d904-119">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="9d904-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="9d904-120">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="9d904-120">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="9d904-124">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="9d904-125">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="9d904-126">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="9d904-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="9d904-127">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="9d904-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="9d904-128">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="9d904-129">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="9d904-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="9d904-130">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="9d904-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="9d904-131">*next* パラメーターを " *呼び出さない* " ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="9d904-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="9d904-132">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="9d904-133">デリゲートが次のデリゲートに要求を渡さない場合、これは " *要求パイプラインのショートサーキット* " と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9d904-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline* .</span></span> <span data-ttu-id="9d904-134">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="9d904-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="9d904-135">たとえば、 [静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、" *ターミナル ミドルウェア* " として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="9d904-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="9d904-136">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="9d904-137">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d904-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="9d904-138">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="9d904-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="9d904-139">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="9d904-140">たとえば、[ヘッダーや状態コードを設定すると、例外がスローされます](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started)。</span><span class="sxs-lookup"><span data-stu-id="9d904-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="9d904-141">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9d904-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="9d904-142">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-142">May cause a protocol violation.</span></span> <span data-ttu-id="9d904-143">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="9d904-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="9d904-144">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-144">May corrupt the body format.</span></span> <span data-ttu-id="9d904-145">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="9d904-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="9d904-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="9d904-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="9d904-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> デリゲートでは、`next` パラメーターは受け取られません。</span><span class="sxs-lookup"><span data-stu-id="9d904-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="9d904-148">最初の `Run` デリゲートが常に終点となり、パイプラインが終了されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="9d904-149">`Run` は規則です。</span><span class="sxs-lookup"><span data-stu-id="9d904-149">`Run` is a convention.</span></span> <span data-ttu-id="9d904-150">一部のミドルウェア コンポーネントでは、パイプラインの最後に実行される `Run[Middleware]` メソッドが公開されることがあります。</span><span class="sxs-lookup"><span data-stu-id="9d904-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="9d904-151">前の例では、`Run` デリゲートによって応答に `"Hello from 2nd delegate."` が書き込まれ、その後、パイプラインが終了となります。</span><span class="sxs-lookup"><span data-stu-id="9d904-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="9d904-152">別の `Use` または `Run` デリゲートが `Run` デリゲートの後に追加される場合、そのデリゲートは呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="9d904-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="9d904-153">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="9d904-153">Middleware order</span></span>

<span data-ttu-id="9d904-154">次の図は、ASP.NET Core MVC と :::no-loc(Razor)::: Pages アプリの完全な要求処理パイプラインを示しています。</span><span class="sxs-lookup"><span data-stu-id="9d904-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and :::no-loc(Razor)::: Pages apps.</span></span> <span data-ttu-id="9d904-155">一般的なアプリでどのように既存のミドルウェアが順序付けされ、どこにカスタム ミドルウェアが追加されるかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="9d904-156">シナリオでの必要性に応じて、既存のミドルウェアの順序を変更したり、新しいカスタム ミドルウェアを挿入したりする方法については、完全に制御できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core のミドルウェア パイプライン](index/_static/middleware-pipeline.svg)

<span data-ttu-id="9d904-158">前の図の **エンドポイント** ミドルウェアでは、対応するアプリの種類 (MVC または :::no-loc(Razor)::: Pages) のフィルター パイプラインが実行されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or :::no-loc(Razor)::: Pages.</span></span>

![ASP.NET Core のフィルター パイプライン](index/_static/mvc-endpoint.svg)

<span data-ttu-id="9d904-160">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="9d904-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="9d904-161">この順序は、セキュリティ、パフォーマンス、および機能にとって **重要** です。</span><span class="sxs-lookup"><span data-stu-id="9d904-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="9d904-162">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-162">The following `Startup.Configure` method adds security-related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="9d904-163">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="9d904-163">In the preceding code:</span></span>

* <span data-ttu-id="9d904-164">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="9d904-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="9d904-165">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="9d904-166">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9d904-166">For example:</span></span>
  * <span data-ttu-id="9d904-167">`UseCors`、`UseAuthentication`、`UseAuthorization` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="9d904-168">現時点では、[こちらのバグ](https://github.com/dotnet/aspnetcore/issues/23218)のため、`UseResponseCaching` の前に `UseCors` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="9d904-169">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-169">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="9d904-170">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="9d904-170">Exception/error handling</span></span>
   * <span data-ttu-id="9d904-171">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="9d904-171">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="9d904-172">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-172">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="9d904-173">データベース エラー ページ ミドルウェアによりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-173">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="9d904-174">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="9d904-174">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="9d904-175">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-175">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="9d904-176">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-176">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="9d904-177">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-177">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="9d904-178">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-178">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="9d904-179">:::no-loc(Cookie)::: ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy%2A>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="9d904-179">:::no-loc(Cookie)::: Policy Middleware (<xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="9d904-180">ルーティング ミドルウェア (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) により、要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-180">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="9d904-181">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-181">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="9d904-182">承認ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) により、ユーザーがセキュリティで保護されたリソースにアクセスすることが承認されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-182">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="9d904-183">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-183">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="9d904-184">アプリでセッション状態が使用されている場合は、:::no-loc(Cookie)::: ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d904-184">If the app uses session state, call Session Middleware after :::no-loc(Cookie)::: Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="9d904-185">エンドポイント ルーティング ミドルウェア (<xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.Map:::no-loc(Razor):::Pages%2A> を含む <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>) により、:::no-loc(Razor)::: Pages エンドポイントが要求パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-185">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.Map:::no-loc(Razor):::Pages%2A>) to add :::no-loc(Razor)::: Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.Use:::no-loc(Cookie):::Policy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.Map:::no-loc(Razor):::Pages();
    });
}
```

<span data-ttu-id="9d904-186">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-186">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="9d904-187">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> です。</span><span class="sxs-lookup"><span data-stu-id="9d904-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="9d904-188">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-188">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="9d904-189">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="9d904-189">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="9d904-190">静的ファイル ミドルウェアでは、承認チェックは提供 **されません** 。</span><span class="sxs-lookup"><span data-stu-id="9d904-190">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="9d904-191">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-191">Any files served by Static File Middleware, including those under *wwwroot* , are publicly available.</span></span> <span data-ttu-id="9d904-192">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d904-192">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="9d904-193">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-193">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="9d904-194">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="9d904-194">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="9d904-195">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の :::no-loc(Razor)::: ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="9d904-195">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific :::no-loc(Razor)::: Page or MVC controller and action.</span></span>

<span data-ttu-id="9d904-196">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="9d904-196">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="9d904-197">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="9d904-197">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="9d904-198">:::no-loc(Razor)::: Pages の応答は圧縮できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-198">The :::no-loc(Razor)::: Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.Map:::no-loc(Razor):::Pages();
    });
}
```

<span data-ttu-id="9d904-199">シングルページ アプリケーション (SPA) の場合、通常はミドルウェア パイプラインの最後に SPA ミドルウェア <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> を配置します。</span><span class="sxs-lookup"><span data-stu-id="9d904-199">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="9d904-200">SPA ミドルウェアは、次のために最後になります。</span><span class="sxs-lookup"><span data-stu-id="9d904-200">The SPA middleware comes last:</span></span>

* <span data-ttu-id="9d904-201">対応する要求に最初にその他のミドルウェアが応答するため。</span><span class="sxs-lookup"><span data-stu-id="9d904-201">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="9d904-202">クライアント側ルーティングを使用する SPA がサーバー アプリに認識されないすべてのルートを実行するため。</span><span class="sxs-lookup"><span data-stu-id="9d904-202">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="9d904-203">SPA の詳細については、[React](xref:spa/react) と [Angular](xref:spa/angular) プロジェクト テンプレートのガイドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d904-203">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="9d904-204">Forwarded Headers Middleware の順序</span><span class="sxs-lookup"><span data-stu-id="9d904-204">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="9d904-205">ミドルウェア パイプラインを分岐する</span><span class="sxs-lookup"><span data-stu-id="9d904-205">Branch the middleware pipeline</span></span>

<span data-ttu-id="9d904-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="9d904-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="9d904-207">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="9d904-207">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="9d904-208">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-208">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="9d904-209">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="9d904-209">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="9d904-210">要求</span><span class="sxs-lookup"><span data-stu-id="9d904-210">Request</span></span>             | <span data-ttu-id="9d904-211">応答</span><span class="sxs-lookup"><span data-stu-id="9d904-211">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="9d904-212">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="9d904-212">localhost:1234</span></span>      | <span data-ttu-id="9d904-213">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="9d904-213">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="9d904-214">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="9d904-214">localhost:1234/map1</span></span> | <span data-ttu-id="9d904-215">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="9d904-215">Map Test 1</span></span>                   |
| <span data-ttu-id="9d904-216">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="9d904-216">localhost:1234/map2</span></span> | <span data-ttu-id="9d904-217">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="9d904-217">Map Test 2</span></span>                   |
| <span data-ttu-id="9d904-218">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="9d904-218">localhost:1234/map3</span></span> | <span data-ttu-id="9d904-219">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="9d904-219">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="9d904-220">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-220">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="9d904-221">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="9d904-221">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="9d904-222">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-222">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="9d904-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="9d904-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="9d904-224">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="9d904-224">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="9d904-225">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-225">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="9d904-226">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="9d904-226">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="9d904-227">要求</span><span class="sxs-lookup"><span data-stu-id="9d904-227">Request</span></span>                       | <span data-ttu-id="9d904-228">応答</span><span class="sxs-lookup"><span data-stu-id="9d904-228">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="9d904-229">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="9d904-229">localhost:1234</span></span>                | <span data-ttu-id="9d904-230">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="9d904-230">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="9d904-231">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="9d904-231">localhost:1234/?branch=master</span></span> | <span data-ttu-id="9d904-232">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="9d904-232">Branch used = master</span></span>         |

<span data-ttu-id="9d904-233">また <xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> では、指定された述語の結果に基づいて、要求パイプラインが分岐されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="9d904-234">`MapWhen` とは異なり、この分岐は、ショートしたり、ターミナル ミドルウェアが含まれたりしなければ、メイン パイプラインに再参加します。</span><span class="sxs-lookup"><span data-stu-id="9d904-234">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="9d904-235">前の例では、"Hello from main pipeline." の応答が</span><span class="sxs-lookup"><span data-stu-id="9d904-235">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="9d904-236">すべての要求に対して書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d904-236">is written for all requests.</span></span> <span data-ttu-id="9d904-237">要求にクエリ文字列変数 `branch` が含まれる場合、メイン パイプラインの再参加前にその値がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-237">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="9d904-238">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="9d904-238">Built-in middleware</span></span>

<span data-ttu-id="9d904-239">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="9d904-239">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="9d904-240">" *順番* " 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-240">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="9d904-241">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは " *ターミナル ミドルウェア* " と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9d904-241">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware* .</span></span> <span data-ttu-id="9d904-242">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d904-242">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="9d904-243">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="9d904-243">Middleware</span></span> | <span data-ttu-id="9d904-244">説明</span><span class="sxs-lookup"><span data-stu-id="9d904-244">Description</span></span> | <span data-ttu-id="9d904-245">順番</span><span class="sxs-lookup"><span data-stu-id="9d904-245">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="9d904-246">認証</span><span class="sxs-lookup"><span data-stu-id="9d904-246">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="9d904-247">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-247">Provides authentication support.</span></span> | <span data-ttu-id="9d904-248">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="9d904-248">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="9d904-249">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-249">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="9d904-250">承認</span><span class="sxs-lookup"><span data-stu-id="9d904-250">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="9d904-251">承認のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-251">Provides authorization support.</span></span> | <span data-ttu-id="9d904-252">認証ミドルウェアの直後。</span><span class="sxs-lookup"><span data-stu-id="9d904-252">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="9d904-253">:::no-loc(Cookie)::: ポリシー</span><span class="sxs-lookup"><span data-stu-id="9d904-253">:::no-loc(Cookie)::: Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="9d904-254">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、:::no-loc(cookie)::: フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="9d904-254">Tracks consent from users for storing personal information and enforces minimum standards for :::no-loc(cookie)::: fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="9d904-255">:::no-loc(cookie)::: を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-255">Before middleware that issues :::no-loc(cookie):::s.</span></span> <span data-ttu-id="9d904-256">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="9d904-256">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="9d904-257">CORS</span><span class="sxs-lookup"><span data-stu-id="9d904-257">CORS</span></span>](xref:security/cors) | <span data-ttu-id="9d904-258">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="9d904-258">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="9d904-259">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-259">Before components that use CORS.</span></span> <span data-ttu-id="9d904-260">現時点では、[こちらのバグ](https://github.com/dotnet/aspnetcore/issues/23218)のため、`UseResponseCaching` の前に `UseCors` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-260">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="9d904-261">診断</span><span class="sxs-lookup"><span data-stu-id="9d904-261">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="9d904-262">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="9d904-262">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="9d904-263">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-263">Before components that generate errors.</span></span> <span data-ttu-id="9d904-264">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-264">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="9d904-265">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="9d904-265">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="9d904-266">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="9d904-266">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="9d904-267">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-267">Before components that consume the updated fields.</span></span> <span data-ttu-id="9d904-268">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="9d904-268">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="9d904-269">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="9d904-269">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="9d904-270">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="9d904-270">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="9d904-271">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-271">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="9d904-272">ヘッダーの伝達</span><span class="sxs-lookup"><span data-stu-id="9d904-272">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="9d904-273">HTTP ヘッダーを受信要求から送信 HTTP クライアント要求に伝達します。</span><span class="sxs-lookup"><span data-stu-id="9d904-273">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="9d904-274">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="9d904-274">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="9d904-275">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="9d904-275">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="9d904-276">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-276">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="9d904-277">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="9d904-277">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="9d904-278">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="9d904-278">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="9d904-279">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-279">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="9d904-280">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="9d904-280">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="9d904-281">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="9d904-281">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="9d904-282">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="9d904-282">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="9d904-283">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="9d904-283">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="9d904-284">MVC</span><span class="sxs-lookup"><span data-stu-id="9d904-284">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="9d904-285">MVC または :::no-loc(Razor)::: Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="9d904-285">Processes requests with MVC/:::no-loc(Razor)::: Pages.</span></span> | <span data-ttu-id="9d904-286">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-286">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="9d904-287">OWIN</span><span class="sxs-lookup"><span data-stu-id="9d904-287">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="9d904-288">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="9d904-288">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="9d904-289">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-289">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="9d904-290">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="9d904-290">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="9d904-291">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-291">Provides support for caching responses.</span></span> | <span data-ttu-id="9d904-292">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-292">Before components that require caching.</span></span> <span data-ttu-id="9d904-293">`UseResponseCaching` の前に `UseCORS` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-293">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="9d904-294">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="9d904-294">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="9d904-295">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-295">Provides support for compressing responses.</span></span> | <span data-ttu-id="9d904-296">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-296">Before components that require compression.</span></span> |
| [<span data-ttu-id="9d904-297">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="9d904-297">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="9d904-298">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-298">Provides localization support.</span></span> | <span data-ttu-id="9d904-299">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-299">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="9d904-300">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="9d904-300">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="9d904-301">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="9d904-301">Defines and constrains request routes.</span></span> | <span data-ttu-id="9d904-302">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-302">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="9d904-303">SPA</span><span class="sxs-lookup"><span data-stu-id="9d904-303">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="9d904-304">シングルページ アプリケーション (SPA) の既定のページを返し、ミドルウェア チェーン内のこの時点以降のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="9d904-304">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="9d904-305">チェーンの終わりで、静的ファイルや MVC アクションなどにサービスを提供する他のミドルウェアが優先されるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="9d904-305">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="9d904-306">セッション</span><span class="sxs-lookup"><span data-stu-id="9d904-306">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="9d904-307">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-307">Provides support for managing user sessions.</span></span> | <span data-ttu-id="9d904-308">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-308">Before components that require Session.</span></span> | 
| [<span data-ttu-id="9d904-309">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="9d904-309">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="9d904-310">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-310">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="9d904-311">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-311">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="9d904-312">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="9d904-312">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="9d904-313">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-313">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="9d904-314">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-314">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="9d904-315">WebSocket</span><span class="sxs-lookup"><span data-stu-id="9d904-315">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="9d904-316">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="9d904-316">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="9d904-317">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-317">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="9d904-318">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9d904-318">Additional resources</span></span>

* <span data-ttu-id="9d904-319">[有効期間と登録のオプション](xref:fundamentals/dependency-injection#lifetime-and-registration-options)には、 *スコープ* 、 *一時* 、 *シングルトン* 有効期間サービスを含むミドルウェアの完全なサンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9d904-319">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* , *transient* , and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9d904-320">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9d904-320">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9d904-321">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="9d904-321">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="9d904-322">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d904-322">Each component:</span></span>

* <span data-ttu-id="9d904-323">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d904-323">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="9d904-324">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-324">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="9d904-325">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-325">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="9d904-326">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="9d904-326">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="9d904-327">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-327">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="9d904-328">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="9d904-328">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="9d904-329">これらの再利用可能なクラスとインラインの匿名メソッドは、" *ミドルウェア* " です。" *ミドルウェア コンポーネント* " とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9d904-329">These reusable classes and in-line anonymous methods are *middleware* , also called *middleware components* .</span></span> <span data-ttu-id="9d904-330">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="9d904-330">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="9d904-331">ショートサーキットしたミドルウェアは " *ターミナル ミドルウェア* " と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="9d904-331">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="9d904-332"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-332"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="9d904-333">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="9d904-333">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="9d904-334">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-334">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="9d904-335">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="9d904-335">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="9d904-336">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="9d904-336">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="9d904-340">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-340">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="9d904-341">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-341">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="9d904-342">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="9d904-342">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="9d904-343">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="9d904-343">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="9d904-344">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-344">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="9d904-345">最初の <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> デリゲートが、パイプラインを終了します。</span><span class="sxs-lookup"><span data-stu-id="9d904-345">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="9d904-346">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="9d904-346">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="9d904-347">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="9d904-347">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="9d904-348">*next* パラメーターを " *呼び出さない* " ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="9d904-348">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="9d904-349">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-349">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="9d904-350">デリゲートが次のデリゲートに要求を渡さない場合、これは " *要求パイプラインのショートサーキット* " と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9d904-350">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline* .</span></span> <span data-ttu-id="9d904-351">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="9d904-351">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="9d904-352">たとえば、 [静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、" *ターミナル ミドルウェア* " として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="9d904-352">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="9d904-353">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-353">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="9d904-354">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d904-354">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="9d904-355">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="9d904-355">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="9d904-356">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-356">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="9d904-357">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-357">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="9d904-358">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9d904-358">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="9d904-359">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-359">May cause a protocol violation.</span></span> <span data-ttu-id="9d904-360">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="9d904-360">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="9d904-361">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-361">May corrupt the body format.</span></span> <span data-ttu-id="9d904-362">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="9d904-362">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="9d904-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="9d904-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="9d904-364">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="9d904-364">Middleware order</span></span>

<span data-ttu-id="9d904-365">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="9d904-365">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="9d904-366">この順序は、セキュリティ、パフォーマンス、および機能にとって **重要** です。</span><span class="sxs-lookup"><span data-stu-id="9d904-366">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="9d904-367">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-367">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="9d904-368">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="9d904-368">In the preceding code:</span></span>

* <span data-ttu-id="9d904-369">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="9d904-369">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="9d904-370">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-370">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="9d904-371">たとえば、`UseCors` と `UseAuthentication` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d904-371">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="9d904-372">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-372">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="9d904-373">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="9d904-373">Exception/error handling</span></span>
   * <span data-ttu-id="9d904-374">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="9d904-374">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="9d904-375">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-375">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="9d904-376">データベース エラー ページ ミドルウェア (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) によりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-376">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="9d904-377">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="9d904-377">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="9d904-378">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-378">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="9d904-379">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-379">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="9d904-380">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-380">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="9d904-381">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-381">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="9d904-382">:::no-loc(Cookie)::: ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy%2A>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="9d904-382">:::no-loc(Cookie)::: Policy Middleware (<xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="9d904-383">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-383">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="9d904-384">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-384">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="9d904-385">アプリでセッション状態が使用されている場合は、:::no-loc(Cookie)::: ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d904-385">If the app uses session state, call Session Middleware after :::no-loc(Cookie)::: Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="9d904-386">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) を使い、要求パイプラインに MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="9d904-386">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.Use:::no-loc(Cookie):::Policy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="9d904-387">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-387">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="9d904-388">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> です。</span><span class="sxs-lookup"><span data-stu-id="9d904-388"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="9d904-389">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="9d904-389">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="9d904-390">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="9d904-390">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="9d904-391">静的ファイル ミドルウェアでは、承認チェックは提供 **されません** 。</span><span class="sxs-lookup"><span data-stu-id="9d904-391">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="9d904-392">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-392">Any files served by Static File Middleware, including those under *wwwroot* , are publicly available.</span></span> <span data-ttu-id="9d904-393">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d904-393">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="9d904-394">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-394">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="9d904-395">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="9d904-395">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="9d904-396">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の :::no-loc(Razor)::: ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="9d904-396">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific :::no-loc(Razor)::: Page or MVC controller and action.</span></span>

<span data-ttu-id="9d904-397">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="9d904-397">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="9d904-398">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="9d904-398">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="9d904-399"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> からの MVC 応答を圧縮することができます。</span><span class="sxs-lookup"><span data-stu-id="9d904-399">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="9d904-400">Use、Run、および Map</span><span class="sxs-lookup"><span data-stu-id="9d904-400">Use, Run, and Map</span></span>

<span data-ttu-id="9d904-401">HTTP パイプラインを構成するには、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> を使います。</span><span class="sxs-lookup"><span data-stu-id="9d904-401">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="9d904-402">`Use` メソッドは、パイプラインをショートさせることができます (つまり `next` 要求デリゲートを呼び出さない場合)。</span><span class="sxs-lookup"><span data-stu-id="9d904-402">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="9d904-403">`Run` は規則であり、一部のミドルウェア コンポーネントは、パイプラインの最後に実行される `Run[Middleware]` メソッドを公開することがあります。</span><span class="sxs-lookup"><span data-stu-id="9d904-403">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="9d904-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="9d904-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="9d904-405">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="9d904-405">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="9d904-406">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-406">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="9d904-407">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="9d904-407">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="9d904-408">要求</span><span class="sxs-lookup"><span data-stu-id="9d904-408">Request</span></span>             | <span data-ttu-id="9d904-409">応答</span><span class="sxs-lookup"><span data-stu-id="9d904-409">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="9d904-410">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="9d904-410">localhost:1234</span></span>      | <span data-ttu-id="9d904-411">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="9d904-411">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="9d904-412">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="9d904-412">localhost:1234/map1</span></span> | <span data-ttu-id="9d904-413">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="9d904-413">Map Test 1</span></span>                   |
| <span data-ttu-id="9d904-414">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="9d904-414">localhost:1234/map2</span></span> | <span data-ttu-id="9d904-415">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="9d904-415">Map Test 2</span></span>                   |
| <span data-ttu-id="9d904-416">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="9d904-416">localhost:1234/map3</span></span> | <span data-ttu-id="9d904-417">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="9d904-417">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="9d904-418">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-418">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="9d904-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="9d904-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="9d904-420">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="9d904-420">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="9d904-421">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d904-421">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="9d904-422">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="9d904-422">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="9d904-423">要求</span><span class="sxs-lookup"><span data-stu-id="9d904-423">Request</span></span>                       | <span data-ttu-id="9d904-424">応答</span><span class="sxs-lookup"><span data-stu-id="9d904-424">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="9d904-425">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="9d904-425">localhost:1234</span></span>                | <span data-ttu-id="9d904-426">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="9d904-426">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="9d904-427">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="9d904-427">localhost:1234/?branch=master</span></span> | <span data-ttu-id="9d904-428">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="9d904-428">Branch used = master</span></span>         |

<span data-ttu-id="9d904-429">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="9d904-429">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="9d904-430">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="9d904-430">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="9d904-431">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="9d904-431">Built-in middleware</span></span>

<span data-ttu-id="9d904-432">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="9d904-432">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="9d904-433">" *順番* " 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="9d904-433">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="9d904-434">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは " *ターミナル ミドルウェア* " と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9d904-434">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware* .</span></span> <span data-ttu-id="9d904-435">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d904-435">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="9d904-436">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="9d904-436">Middleware</span></span> | <span data-ttu-id="9d904-437">説明</span><span class="sxs-lookup"><span data-stu-id="9d904-437">Description</span></span> | <span data-ttu-id="9d904-438">順番</span><span class="sxs-lookup"><span data-stu-id="9d904-438">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="9d904-439">認証</span><span class="sxs-lookup"><span data-stu-id="9d904-439">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="9d904-440">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-440">Provides authentication support.</span></span> | <span data-ttu-id="9d904-441">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="9d904-441">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="9d904-442">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-442">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="9d904-443">:::no-loc(Cookie)::: ポリシー</span><span class="sxs-lookup"><span data-stu-id="9d904-443">:::no-loc(Cookie)::: Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="9d904-444">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、:::no-loc(cookie)::: フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="9d904-444">Tracks consent from users for storing personal information and enforces minimum standards for :::no-loc(cookie)::: fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="9d904-445">:::no-loc(cookie)::: を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-445">Before middleware that issues :::no-loc(cookie):::s.</span></span> <span data-ttu-id="9d904-446">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="9d904-446">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="9d904-447">CORS</span><span class="sxs-lookup"><span data-stu-id="9d904-447">CORS</span></span>](xref:security/cors) | <span data-ttu-id="9d904-448">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="9d904-448">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="9d904-449">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-449">Before components that use CORS.</span></span> |
| [<span data-ttu-id="9d904-450">診断</span><span class="sxs-lookup"><span data-stu-id="9d904-450">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="9d904-451">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="9d904-451">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="9d904-452">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-452">Before components that generate errors.</span></span> <span data-ttu-id="9d904-453">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-453">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="9d904-454">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="9d904-454">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="9d904-455">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="9d904-455">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="9d904-456">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-456">Before components that consume the updated fields.</span></span> <span data-ttu-id="9d904-457">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="9d904-457">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="9d904-458">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="9d904-458">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="9d904-459">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="9d904-459">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="9d904-460">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-460">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="9d904-461">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="9d904-461">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="9d904-462">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="9d904-462">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="9d904-463">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-463">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="9d904-464">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="9d904-464">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="9d904-465">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="9d904-465">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="9d904-466">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-466">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="9d904-467">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="9d904-467">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="9d904-468">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="9d904-468">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="9d904-469">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="9d904-469">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="9d904-470">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="9d904-470">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="9d904-471">MVC</span><span class="sxs-lookup"><span data-stu-id="9d904-471">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="9d904-472">MVC または :::no-loc(Razor)::: Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="9d904-472">Processes requests with MVC/:::no-loc(Razor)::: Pages.</span></span> | <span data-ttu-id="9d904-473">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-473">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="9d904-474">OWIN</span><span class="sxs-lookup"><span data-stu-id="9d904-474">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="9d904-475">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="9d904-475">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="9d904-476">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-476">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="9d904-477">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="9d904-477">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="9d904-478">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-478">Provides support for caching responses.</span></span> | <span data-ttu-id="9d904-479">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-479">Before components that require caching.</span></span> |
| [<span data-ttu-id="9d904-480">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="9d904-480">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="9d904-481">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-481">Provides support for compressing responses.</span></span> | <span data-ttu-id="9d904-482">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-482">Before components that require compression.</span></span> |
| [<span data-ttu-id="9d904-483">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="9d904-483">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="9d904-484">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-484">Provides localization support.</span></span> | <span data-ttu-id="9d904-485">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-485">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="9d904-486">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="9d904-486">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="9d904-487">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="9d904-487">Defines and constrains request routes.</span></span> | <span data-ttu-id="9d904-488">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-488">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="9d904-489">セッション</span><span class="sxs-lookup"><span data-stu-id="9d904-489">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="9d904-490">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-490">Provides support for managing user sessions.</span></span> | <span data-ttu-id="9d904-491">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-491">Before components that require Session.</span></span> |
| [<span data-ttu-id="9d904-492">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="9d904-492">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="9d904-493">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-493">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="9d904-494">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="9d904-494">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="9d904-495">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="9d904-495">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="9d904-496">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9d904-496">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="9d904-497">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-497">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="9d904-498">WebSocket</span><span class="sxs-lookup"><span data-stu-id="9d904-498">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="9d904-499">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="9d904-499">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="9d904-500">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="9d904-500">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="9d904-501">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9d904-501">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
