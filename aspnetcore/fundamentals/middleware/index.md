---
title: ASP.NET Core のミドルウェア
author: rick-anderson
description: ASP.NET Core のミドルウェアと要求パイプラインについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/index
ms.openlocfilehash: 81a0da65215bc41f6dfad0de28a95bdc455bd8fb
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292790"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="325ca-103">ASP.NET Core のミドルウェア</span><span class="sxs-lookup"><span data-stu-id="325ca-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="325ca-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="325ca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="325ca-105">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="325ca-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="325ca-106">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="325ca-106">Each component:</span></span>

* <span data-ttu-id="325ca-107">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="325ca-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="325ca-108">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="325ca-109">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="325ca-110">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="325ca-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="325ca-111">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="325ca-112">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="325ca-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="325ca-113">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="325ca-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="325ca-114">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="325ca-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="325ca-115">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="325ca-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="325ca-116"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="325ca-117">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="325ca-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="325ca-118">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="325ca-119">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="325ca-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="325ca-120">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="325ca-120">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="325ca-124">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="325ca-125">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="325ca-126">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="325ca-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="325ca-127">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="325ca-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="325ca-128">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="325ca-129">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="325ca-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="325ca-130">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="325ca-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="325ca-131">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="325ca-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="325ca-132">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="325ca-133">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="325ca-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="325ca-134">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="325ca-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="325ca-135">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="325ca-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="325ca-136">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="325ca-137">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="325ca-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="325ca-138">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="325ca-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="325ca-139">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="325ca-140">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-140">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="325ca-141">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="325ca-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="325ca-142">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-142">May cause a protocol violation.</span></span> <span data-ttu-id="325ca-143">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="325ca-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="325ca-144">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-144">May corrupt the body format.</span></span> <span data-ttu-id="325ca-145">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="325ca-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="325ca-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="325ca-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="325ca-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> デリゲートでは、`next` パラメーターは受け取られません。</span><span class="sxs-lookup"><span data-stu-id="325ca-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="325ca-148">最初の `Run` デリゲートが常に終点となり、パイプラインが終了されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="325ca-149">`Run` は規則です。</span><span class="sxs-lookup"><span data-stu-id="325ca-149">`Run` is a convention.</span></span> <span data-ttu-id="325ca-150">一部のミドルウェア コンポーネントでは、パイプラインの最後に実行される `Run[Middleware]` メソッドが公開されることがあります。</span><span class="sxs-lookup"><span data-stu-id="325ca-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="325ca-151">前の例では、`Run` デリゲートによって応答に `"Hello from 2nd delegate."` が書き込まれ、その後、パイプラインが終了となります。</span><span class="sxs-lookup"><span data-stu-id="325ca-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="325ca-152">別の `Use` または `Run` デリゲートが `Run` デリゲートの後に追加される場合、そのデリゲートは呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="325ca-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="325ca-153">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="325ca-153">Middleware order</span></span>

<span data-ttu-id="325ca-154">次の図は、ASP.NET Core MVC と Razor Pages アプリの完全な要求処理パイプラインを示しています。</span><span class="sxs-lookup"><span data-stu-id="325ca-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="325ca-155">一般的なアプリでどのように既存のミドルウェアが順序付けされ、どこにカスタム ミドルウェアが追加されるかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="325ca-156">シナリオでの必要性に応じて、既存のミドルウェアの順序を変更したり、新しいカスタム ミドルウェアを挿入したりする方法については、完全に制御できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core のミドルウェア パイプライン](index/_static/middleware-pipeline.svg)

<span data-ttu-id="325ca-158">前の図の**エンドポイント** ミドルウェアでは、対応するアプリの種類 (MVC または Razor Pages) のフィルター パイプラインが実行されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core のフィルター パイプライン](index/_static/mvc-endpoint.svg)

<span data-ttu-id="325ca-160">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="325ca-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="325ca-161">この順序は、セキュリティ、パフォーマンス、および機能にとって**重要**です。</span><span class="sxs-lookup"><span data-stu-id="325ca-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="325ca-162">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-162">The following `Startup.Configure` method adds security-related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="325ca-163">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="325ca-163">In the preceding code:</span></span>

* <span data-ttu-id="325ca-164">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="325ca-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="325ca-165">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="325ca-166">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="325ca-166">For example:</span></span>
  * <span data-ttu-id="325ca-167">`UseCors`、`UseAuthentication`、`UseAuthorization` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="325ca-168">現時点では、[こちらのバグ](https://github.com/dotnet/aspnetcore/issues/23218)のため、`UseResponseCaching` の前に `UseCors` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="325ca-169">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-169">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="325ca-170">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="325ca-170">Exception/error handling</span></span>
   * <span data-ttu-id="325ca-171">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="325ca-171">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="325ca-172">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-172">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="325ca-173">データベース エラー ページ ミドルウェアによりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-173">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="325ca-174">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="325ca-174">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="325ca-175">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-175">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="325ca-176">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-176">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="325ca-177">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-177">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="325ca-178">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-178">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="325ca-179">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="325ca-179">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="325ca-180">ルーティング ミドルウェア (`UseRouting`) により、要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-180">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="325ca-181">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-181">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="325ca-182">承認ミドルウェア (`UseAuthorization`) により、ユーザーがセキュリティで保護されたリソースにアクセスすることが承認されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-182">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="325ca-183">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-183">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="325ca-184">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="325ca-184">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="325ca-185">エンドポイント ルーティング ミドルウェア (`MapRazorPages` を含む `UseEndpoints`) により、Razor Pages エンドポイントが要求パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-185">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="325ca-186">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-186">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="325ca-187">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="325ca-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="325ca-188">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-188">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="325ca-189">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="325ca-189">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="325ca-190">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="325ca-190">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="325ca-191">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-191">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="325ca-192">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="325ca-192">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="325ca-193">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-193">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="325ca-194">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="325ca-194">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="325ca-195">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="325ca-195">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="325ca-196">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="325ca-196">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="325ca-197">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="325ca-197">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="325ca-198">Razor Pages の応答は圧縮できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-198">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="325ca-199">シングルページ アプリケーション (SPA) の場合、通常はミドルウェア パイプラインの最後に SPA ミドルウェア <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> を配置します。</span><span class="sxs-lookup"><span data-stu-id="325ca-199">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="325ca-200">SPA ミドルウェアは、次のために最後になります。</span><span class="sxs-lookup"><span data-stu-id="325ca-200">The SPA middleware comes last:</span></span>

* <span data-ttu-id="325ca-201">対応する要求に最初にその他のミドルウェアが応答するため。</span><span class="sxs-lookup"><span data-stu-id="325ca-201">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="325ca-202">クライアント側ルーティングを使用する SPA がサーバー アプリに認識されないすべてのルートを実行するため。</span><span class="sxs-lookup"><span data-stu-id="325ca-202">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="325ca-203">SPA の詳細については、[React](xref:spa/react) と [Angular](xref:spa/angular) プロジェクト テンプレートのガイドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="325ca-203">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="325ca-204">Forwarded Headers Middleware の順序</span><span class="sxs-lookup"><span data-stu-id="325ca-204">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="325ca-205">ミドルウェア パイプラインを分岐する</span><span class="sxs-lookup"><span data-stu-id="325ca-205">Branch the middleware pipeline</span></span>

<span data-ttu-id="325ca-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="325ca-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="325ca-207">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="325ca-207">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="325ca-208">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-208">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="325ca-209">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="325ca-209">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="325ca-210">要求</span><span class="sxs-lookup"><span data-stu-id="325ca-210">Request</span></span>             | <span data-ttu-id="325ca-211">応答</span><span class="sxs-lookup"><span data-stu-id="325ca-211">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="325ca-212">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="325ca-212">localhost:1234</span></span>      | <span data-ttu-id="325ca-213">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="325ca-213">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="325ca-214">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="325ca-214">localhost:1234/map1</span></span> | <span data-ttu-id="325ca-215">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="325ca-215">Map Test 1</span></span>                   |
| <span data-ttu-id="325ca-216">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="325ca-216">localhost:1234/map2</span></span> | <span data-ttu-id="325ca-217">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="325ca-217">Map Test 2</span></span>                   |
| <span data-ttu-id="325ca-218">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="325ca-218">localhost:1234/map3</span></span> | <span data-ttu-id="325ca-219">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="325ca-219">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="325ca-220">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-220">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="325ca-221">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="325ca-221">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="325ca-222">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-222">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="325ca-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="325ca-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="325ca-224">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="325ca-224">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="325ca-225">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-225">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="325ca-226">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="325ca-226">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="325ca-227">要求</span><span class="sxs-lookup"><span data-stu-id="325ca-227">Request</span></span>                       | <span data-ttu-id="325ca-228">応答</span><span class="sxs-lookup"><span data-stu-id="325ca-228">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="325ca-229">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="325ca-229">localhost:1234</span></span>                | <span data-ttu-id="325ca-230">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="325ca-230">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="325ca-231">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="325ca-231">localhost:1234/?branch=master</span></span> | <span data-ttu-id="325ca-232">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="325ca-232">Branch used = master</span></span>         |

<span data-ttu-id="325ca-233">また <xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> では、指定された述語の結果に基づいて、要求パイプラインが分岐されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="325ca-234">`MapWhen` とは異なり、この分岐は、ショートしたり、ターミナル ミドルウェアが含まれたりしなければ、メイン パイプラインに再参加します。</span><span class="sxs-lookup"><span data-stu-id="325ca-234">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="325ca-235">前の例では、"Hello from main pipeline." の応答が</span><span class="sxs-lookup"><span data-stu-id="325ca-235">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="325ca-236">すべての要求に対して書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="325ca-236">is written for all requests.</span></span> <span data-ttu-id="325ca-237">要求にクエリ文字列変数 `branch` が含まれる場合、メイン パイプラインの再参加前にその値がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-237">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="325ca-238">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="325ca-238">Built-in middleware</span></span>

<span data-ttu-id="325ca-239">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="325ca-239">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="325ca-240">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-240">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="325ca-241">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="325ca-241">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="325ca-242">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="325ca-242">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="325ca-243">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="325ca-243">Middleware</span></span> | <span data-ttu-id="325ca-244">説明</span><span class="sxs-lookup"><span data-stu-id="325ca-244">Description</span></span> | <span data-ttu-id="325ca-245">順番</span><span class="sxs-lookup"><span data-stu-id="325ca-245">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="325ca-246">認証</span><span class="sxs-lookup"><span data-stu-id="325ca-246">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="325ca-247">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-247">Provides authentication support.</span></span> | <span data-ttu-id="325ca-248">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="325ca-248">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="325ca-249">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-249">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="325ca-250">承認</span><span class="sxs-lookup"><span data-stu-id="325ca-250">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | <span data-ttu-id="325ca-251">承認のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-251">Provides authorization support.</span></span> | <span data-ttu-id="325ca-252">認証ミドルウェアの直後。</span><span class="sxs-lookup"><span data-stu-id="325ca-252">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="325ca-253">Cookie のポリシー</span><span class="sxs-lookup"><span data-stu-id="325ca-253">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="325ca-254">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、Cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="325ca-254">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="325ca-255">Cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-255">Before middleware that issues cookies.</span></span> <span data-ttu-id="325ca-256">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="325ca-256">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="325ca-257">CORS</span><span class="sxs-lookup"><span data-stu-id="325ca-257">CORS</span></span>](xref:security/cors) | <span data-ttu-id="325ca-258">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="325ca-258">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="325ca-259">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-259">Before components that use CORS.</span></span> <span data-ttu-id="325ca-260">現時点では、[こちらのバグ](https://github.com/dotnet/aspnetcore/issues/23218)のため、`UseResponseCaching` の前に `UseCors` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-260">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="325ca-261">診断</span><span class="sxs-lookup"><span data-stu-id="325ca-261">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="325ca-262">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="325ca-262">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="325ca-263">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-263">Before components that generate errors.</span></span> <span data-ttu-id="325ca-264">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-264">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="325ca-265">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="325ca-265">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="325ca-266">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="325ca-266">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="325ca-267">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-267">Before components that consume the updated fields.</span></span> <span data-ttu-id="325ca-268">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="325ca-268">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="325ca-269">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="325ca-269">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="325ca-270">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="325ca-270">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="325ca-271">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-271">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="325ca-272">ヘッダーの伝達</span><span class="sxs-lookup"><span data-stu-id="325ca-272">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="325ca-273">HTTP ヘッダーを受信要求から送信 HTTP クライアント要求に伝達します。</span><span class="sxs-lookup"><span data-stu-id="325ca-273">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="325ca-274">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="325ca-274">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="325ca-275">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="325ca-275">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="325ca-276">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-276">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="325ca-277">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="325ca-277">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="325ca-278">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="325ca-278">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="325ca-279">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-279">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="325ca-280">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="325ca-280">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="325ca-281">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="325ca-281">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="325ca-282">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="325ca-282">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="325ca-283">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="325ca-283">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="325ca-284">MVC</span><span class="sxs-lookup"><span data-stu-id="325ca-284">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="325ca-285">MVC または Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="325ca-285">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="325ca-286">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-286">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="325ca-287">OWIN</span><span class="sxs-lookup"><span data-stu-id="325ca-287">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="325ca-288">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="325ca-288">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="325ca-289">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-289">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="325ca-290">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="325ca-290">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="325ca-291">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-291">Provides support for caching responses.</span></span> | <span data-ttu-id="325ca-292">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-292">Before components that require caching.</span></span> <span data-ttu-id="325ca-293">`UseResponseCaching` の前に `UseCORS` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-293">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="325ca-294">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="325ca-294">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="325ca-295">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-295">Provides support for compressing responses.</span></span> | <span data-ttu-id="325ca-296">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-296">Before components that require compression.</span></span> |
| [<span data-ttu-id="325ca-297">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="325ca-297">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="325ca-298">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-298">Provides localization support.</span></span> | <span data-ttu-id="325ca-299">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-299">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="325ca-300">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="325ca-300">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="325ca-301">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="325ca-301">Defines and constrains request routes.</span></span> | <span data-ttu-id="325ca-302">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-302">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="325ca-303">SPA</span><span class="sxs-lookup"><span data-stu-id="325ca-303">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa*) | <span data-ttu-id="325ca-304">シングルページ アプリケーション (SPA) の既定のページを返し、ミドルウェア チェーン内のこの時点以降のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="325ca-304">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="325ca-305">チェーンの終わりで、静的ファイルや MVC アクションなどにサービスを提供する他のミドルウェアが優先されるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="325ca-305">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="325ca-306">セッション</span><span class="sxs-lookup"><span data-stu-id="325ca-306">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="325ca-307">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-307">Provides support for managing user sessions.</span></span> | <span data-ttu-id="325ca-308">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-308">Before components that require Session.</span></span> | 
| [<span data-ttu-id="325ca-309">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="325ca-309">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="325ca-310">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-310">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="325ca-311">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-311">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="325ca-312">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="325ca-312">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="325ca-313">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-313">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="325ca-314">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-314">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="325ca-315">WebSocket</span><span class="sxs-lookup"><span data-stu-id="325ca-315">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="325ca-316">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="325ca-316">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="325ca-317">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-317">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="325ca-318">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="325ca-318">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="325ca-319">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="325ca-319">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="325ca-320">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="325ca-320">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="325ca-321">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="325ca-321">Each component:</span></span>

* <span data-ttu-id="325ca-322">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="325ca-322">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="325ca-323">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-323">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="325ca-324">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-324">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="325ca-325">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="325ca-325">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="325ca-326">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-326">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="325ca-327">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="325ca-327">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="325ca-328">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="325ca-328">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="325ca-329">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="325ca-329">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="325ca-330">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="325ca-330">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="325ca-331"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-331"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="325ca-332">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="325ca-332">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="325ca-333">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-333">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="325ca-334">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="325ca-334">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="325ca-335">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="325ca-335">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="325ca-339">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-339">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="325ca-340">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-340">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="325ca-341">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="325ca-341">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="325ca-342">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="325ca-342">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="325ca-343">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-343">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="325ca-344">最初の <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> デリゲートが、パイプラインを終了します。</span><span class="sxs-lookup"><span data-stu-id="325ca-344">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="325ca-345">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="325ca-345">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="325ca-346">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="325ca-346">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="325ca-347">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="325ca-347">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="325ca-348">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-348">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="325ca-349">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="325ca-349">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="325ca-350">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="325ca-350">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="325ca-351">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="325ca-351">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="325ca-352">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-352">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="325ca-353">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="325ca-353">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="325ca-354">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="325ca-354">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="325ca-355">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-355">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="325ca-356">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-356">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="325ca-357">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="325ca-357">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="325ca-358">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-358">May cause a protocol violation.</span></span> <span data-ttu-id="325ca-359">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="325ca-359">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="325ca-360">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-360">May corrupt the body format.</span></span> <span data-ttu-id="325ca-361">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="325ca-361">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="325ca-362"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="325ca-362"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="325ca-363">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="325ca-363">Middleware order</span></span>

<span data-ttu-id="325ca-364">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="325ca-364">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="325ca-365">この順序は、セキュリティ、パフォーマンス、および機能にとって**重要**です。</span><span class="sxs-lookup"><span data-stu-id="325ca-365">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="325ca-366">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-366">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="325ca-367">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="325ca-367">In the preceding code:</span></span>

* <span data-ttu-id="325ca-368">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="325ca-368">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="325ca-369">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-369">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="325ca-370">たとえば、`UseCors` と `UseAuthentication` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="325ca-370">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="325ca-371">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-371">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="325ca-372">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="325ca-372">Exception/error handling</span></span>
   * <span data-ttu-id="325ca-373">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="325ca-373">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="325ca-374">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-374">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="325ca-375">データベース エラー ページ ミドルウェア (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) によりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-375">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="325ca-376">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="325ca-376">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="325ca-377">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-377">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="325ca-378">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-378">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="325ca-379">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-379">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="325ca-380">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-380">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="325ca-381">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="325ca-381">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="325ca-382">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-382">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="325ca-383">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-383">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="325ca-384">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="325ca-384">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="325ca-385">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) を使い、要求パイプラインに MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="325ca-385">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="325ca-386">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-386">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="325ca-387">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="325ca-387"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="325ca-388">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="325ca-388">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="325ca-389">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="325ca-389">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="325ca-390">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="325ca-390">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="325ca-391">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-391">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="325ca-392">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="325ca-392">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="325ca-393">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-393">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="325ca-394">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="325ca-394">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="325ca-395">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="325ca-395">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="325ca-396">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="325ca-396">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="325ca-397">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="325ca-397">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="325ca-398"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> からの MVC 応答を圧縮することができます。</span><span class="sxs-lookup"><span data-stu-id="325ca-398">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="325ca-399">Use、Run、および Map</span><span class="sxs-lookup"><span data-stu-id="325ca-399">Use, Run, and Map</span></span>

<span data-ttu-id="325ca-400">HTTP パイプラインを構成するには、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> を使います。</span><span class="sxs-lookup"><span data-stu-id="325ca-400">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="325ca-401">`Use` メソッドは、パイプラインをショートさせることができます (つまり `next` 要求デリゲートを呼び出さない場合)。</span><span class="sxs-lookup"><span data-stu-id="325ca-401">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="325ca-402">`Run` は規則であり、一部のミドルウェア コンポーネントは、パイプラインの最後に実行される `Run[Middleware]` メソッドを公開することがあります。</span><span class="sxs-lookup"><span data-stu-id="325ca-402">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="325ca-403"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="325ca-403"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="325ca-404">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="325ca-404">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="325ca-405">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-405">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="325ca-406">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="325ca-406">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="325ca-407">要求</span><span class="sxs-lookup"><span data-stu-id="325ca-407">Request</span></span>             | <span data-ttu-id="325ca-408">応答</span><span class="sxs-lookup"><span data-stu-id="325ca-408">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="325ca-409">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="325ca-409">localhost:1234</span></span>      | <span data-ttu-id="325ca-410">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="325ca-410">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="325ca-411">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="325ca-411">localhost:1234/map1</span></span> | <span data-ttu-id="325ca-412">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="325ca-412">Map Test 1</span></span>                   |
| <span data-ttu-id="325ca-413">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="325ca-413">localhost:1234/map2</span></span> | <span data-ttu-id="325ca-414">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="325ca-414">Map Test 2</span></span>                   |
| <span data-ttu-id="325ca-415">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="325ca-415">localhost:1234/map3</span></span> | <span data-ttu-id="325ca-416">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="325ca-416">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="325ca-417">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-417">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="325ca-418"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="325ca-418"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="325ca-419">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="325ca-419">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="325ca-420">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="325ca-420">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="325ca-421">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="325ca-421">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="325ca-422">要求</span><span class="sxs-lookup"><span data-stu-id="325ca-422">Request</span></span>                       | <span data-ttu-id="325ca-423">応答</span><span class="sxs-lookup"><span data-stu-id="325ca-423">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="325ca-424">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="325ca-424">localhost:1234</span></span>                | <span data-ttu-id="325ca-425">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="325ca-425">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="325ca-426">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="325ca-426">localhost:1234/?branch=master</span></span> | <span data-ttu-id="325ca-427">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="325ca-427">Branch used = master</span></span>         |

<span data-ttu-id="325ca-428">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="325ca-428">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="325ca-429">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="325ca-429">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="325ca-430">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="325ca-430">Built-in middleware</span></span>

<span data-ttu-id="325ca-431">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="325ca-431">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="325ca-432">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="325ca-432">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="325ca-433">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="325ca-433">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="325ca-434">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="325ca-434">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="325ca-435">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="325ca-435">Middleware</span></span> | <span data-ttu-id="325ca-436">説明</span><span class="sxs-lookup"><span data-stu-id="325ca-436">Description</span></span> | <span data-ttu-id="325ca-437">順番</span><span class="sxs-lookup"><span data-stu-id="325ca-437">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="325ca-438">認証</span><span class="sxs-lookup"><span data-stu-id="325ca-438">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="325ca-439">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-439">Provides authentication support.</span></span> | <span data-ttu-id="325ca-440">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="325ca-440">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="325ca-441">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-441">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="325ca-442">Cookie のポリシー</span><span class="sxs-lookup"><span data-stu-id="325ca-442">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="325ca-443">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、Cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="325ca-443">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="325ca-444">Cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-444">Before middleware that issues cookies.</span></span> <span data-ttu-id="325ca-445">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="325ca-445">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="325ca-446">CORS</span><span class="sxs-lookup"><span data-stu-id="325ca-446">CORS</span></span>](xref:security/cors) | <span data-ttu-id="325ca-447">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="325ca-447">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="325ca-448">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-448">Before components that use CORS.</span></span> |
| [<span data-ttu-id="325ca-449">診断</span><span class="sxs-lookup"><span data-stu-id="325ca-449">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="325ca-450">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="325ca-450">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="325ca-451">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-451">Before components that generate errors.</span></span> <span data-ttu-id="325ca-452">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-452">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="325ca-453">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="325ca-453">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="325ca-454">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="325ca-454">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="325ca-455">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-455">Before components that consume the updated fields.</span></span> <span data-ttu-id="325ca-456">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="325ca-456">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="325ca-457">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="325ca-457">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="325ca-458">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="325ca-458">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="325ca-459">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-459">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="325ca-460">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="325ca-460">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="325ca-461">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="325ca-461">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="325ca-462">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-462">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="325ca-463">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="325ca-463">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="325ca-464">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="325ca-464">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="325ca-465">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-465">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="325ca-466">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="325ca-466">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="325ca-467">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="325ca-467">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="325ca-468">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="325ca-468">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="325ca-469">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="325ca-469">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="325ca-470">MVC</span><span class="sxs-lookup"><span data-stu-id="325ca-470">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="325ca-471">MVC または Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="325ca-471">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="325ca-472">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-472">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="325ca-473">OWIN</span><span class="sxs-lookup"><span data-stu-id="325ca-473">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="325ca-474">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="325ca-474">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="325ca-475">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-475">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="325ca-476">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="325ca-476">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="325ca-477">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-477">Provides support for caching responses.</span></span> | <span data-ttu-id="325ca-478">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-478">Before components that require caching.</span></span> |
| [<span data-ttu-id="325ca-479">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="325ca-479">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="325ca-480">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-480">Provides support for compressing responses.</span></span> | <span data-ttu-id="325ca-481">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-481">Before components that require compression.</span></span> |
| [<span data-ttu-id="325ca-482">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="325ca-482">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="325ca-483">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-483">Provides localization support.</span></span> | <span data-ttu-id="325ca-484">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-484">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="325ca-485">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="325ca-485">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="325ca-486">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="325ca-486">Defines and constrains request routes.</span></span> | <span data-ttu-id="325ca-487">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-487">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="325ca-488">セッション</span><span class="sxs-lookup"><span data-stu-id="325ca-488">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="325ca-489">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-489">Provides support for managing user sessions.</span></span> | <span data-ttu-id="325ca-490">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-490">Before components that require Session.</span></span> |
| [<span data-ttu-id="325ca-491">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="325ca-491">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="325ca-492">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-492">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="325ca-493">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="325ca-493">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="325ca-494">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="325ca-494">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="325ca-495">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="325ca-495">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="325ca-496">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-496">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="325ca-497">WebSocket</span><span class="sxs-lookup"><span data-stu-id="325ca-497">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="325ca-498">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="325ca-498">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="325ca-499">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="325ca-499">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="325ca-500">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="325ca-500">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
