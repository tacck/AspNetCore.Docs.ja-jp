---
title: ASP.NET Core のミドルウェア
author: rick-anderson
description: ASP.NET Core のミドルウェアと要求パイプラインについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/index
ms.openlocfilehash: aa51e53284bc25629b3975ff0e6de967b9a2b866
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513123"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="f025e-103">ASP.NET Core のミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f025e-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f025e-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f025e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f025e-105">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="f025e-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="f025e-106">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="f025e-106">Each component:</span></span>

* <span data-ttu-id="f025e-107">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="f025e-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="f025e-108">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="f025e-109">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="f025e-110">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="f025e-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="f025e-111">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="f025e-112">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="f025e-113">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f025e-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="f025e-114">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="f025e-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="f025e-115">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="f025e-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="f025e-116"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="f025e-117">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="f025e-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="f025e-118">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="f025e-119">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="f025e-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="f025e-120">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="f025e-120">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="f025e-124">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="f025e-125">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="f025e-126">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="f025e-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="f025e-127">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="f025e-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="f025e-128">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="f025e-129">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="f025e-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="f025e-130">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="f025e-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="f025e-131">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="f025e-132">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="f025e-133">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f025e-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="f025e-134">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="f025e-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="f025e-135">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="f025e-136">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="f025e-137">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f025e-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="f025e-138">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="f025e-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="f025e-139">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="f025e-140">たとえば、[ヘッダーや状態コードを設定すると、例外がスローされます](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started)。</span><span class="sxs-lookup"><span data-stu-id="f025e-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="f025e-141">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f025e-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="f025e-142">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-142">May cause a protocol violation.</span></span> <span data-ttu-id="f025e-143">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="f025e-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="f025e-144">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-144">May corrupt the body format.</span></span> <span data-ttu-id="f025e-145">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="f025e-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="f025e-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="f025e-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="f025e-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> デリゲートでは、`next` パラメーターは受け取られません。</span><span class="sxs-lookup"><span data-stu-id="f025e-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="f025e-148">最初の `Run` デリゲートが常に終点となり、パイプラインが終了されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="f025e-149">`Run` は規則です。</span><span class="sxs-lookup"><span data-stu-id="f025e-149">`Run` is a convention.</span></span> <span data-ttu-id="f025e-150">一部のミドルウェア コンポーネントでは、パイプラインの最後に実行される `Run[Middleware]` メソッドが公開されることがあります。</span><span class="sxs-lookup"><span data-stu-id="f025e-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f025e-151">前の例では、`Run` デリゲートによって応答に `"Hello from 2nd delegate."` が書き込まれ、その後、パイプラインが終了となります。</span><span class="sxs-lookup"><span data-stu-id="f025e-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="f025e-152">別の `Use` または `Run` デリゲートが `Run` デリゲートの後に追加される場合、そのデリゲートは呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="f025e-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="f025e-153">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="f025e-153">Middleware order</span></span>

<span data-ttu-id="f025e-154">次の図は、ASP.NET Core MVC と Razor Pages アプリの完全な要求処理パイプラインを示しています。</span><span class="sxs-lookup"><span data-stu-id="f025e-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="f025e-155">一般的なアプリでどのように既存のミドルウェアが順序付けされ、どこにカスタム ミドルウェアが追加されるかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="f025e-156">シナリオでの必要性に応じて、既存のミドルウェアの順序を変更したり、新しいカスタム ミドルウェアを挿入したりする方法については、完全に制御できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core のミドルウェア パイプライン](index/_static/middleware-pipeline.svg)

<span data-ttu-id="f025e-158">前の図の **エンドポイント** ミドルウェアでは、対応するアプリの種類 (MVC または Razor Pages) のフィルター パイプラインが実行されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core のフィルター パイプライン](index/_static/mvc-endpoint.svg)

<span data-ttu-id="f025e-160">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="f025e-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="f025e-161">この順序は、セキュリティ、パフォーマンス、および機能にとって **重要** です。</span><span class="sxs-lookup"><span data-stu-id="f025e-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="f025e-162">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが、一般的な推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-162">The following `Startup.Configure` method adds security-related middleware components in the typical recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="f025e-163">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="f025e-163">In the preceding code:</span></span>

* <span data-ttu-id="f025e-164">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="f025e-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="f025e-165">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="f025e-166">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f025e-166">For example:</span></span>
  * <span data-ttu-id="f025e-167">`UseCors`、`UseAuthentication`、`UseAuthorization` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="f025e-168">現時点では、[こちらのバグ](https://github.com/dotnet/aspnetcore/issues/23218)のため、`UseResponseCaching` の前に `UseCors` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="f025e-169">一部のシナリオでは、ミドルウェアの順序が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-169">In some scenarios, middleware will have different ordering.</span></span> <span data-ttu-id="f025e-170">たとえば、キャッシュと圧縮の順序はシナリオ固有のものであり、複数の有効な順序があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-170">For example, caching and compression ordering is scenario specific, and there's multiple valid orderings.</span></span> <span data-ttu-id="f025e-171">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f025e-171">For example:</span></span>

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
```

<span data-ttu-id="f025e-172">上記のコードでは、圧縮された応答をキャッシュすることによって CPU を節約できますが、gzip や brotli などの異なる圧縮アルゴリズムを使用してリソースの複数の表現をキャッシュすることができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-172">With the preceding code, CPU could be saved by caching the compressed response, but you might end up caching multiple representations of a resource using different compression algorithms such as gzip or brotli.</span></span>

<span data-ttu-id="f025e-173">次の順序では、圧縮された静的ファイルをキャッシュできるように、静的ファイルが結合されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-173">The following ordering combines static files to allow caching compressed static files:</span></span>

```csharp
app.UseResponseCaching
app.UseResponseCompression
app.UseStaticFiles
```

<span data-ttu-id="f025e-174">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-174">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="f025e-175">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="f025e-175">Exception/error handling</span></span>
   * <span data-ttu-id="f025e-176">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="f025e-176">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="f025e-177">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-177">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="f025e-178">データベース エラー ページ ミドルウェアによりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-178">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="f025e-179">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="f025e-179">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="f025e-180">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-180">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="f025e-181">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-181">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="f025e-182">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-182">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="f025e-183">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-183">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="f025e-184">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="f025e-184">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="f025e-185">ルーティング ミドルウェア (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) により、要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-185">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="f025e-186">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-186">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="f025e-187">承認ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) により、ユーザーがセキュリティで保護されたリソースにアクセスすることが承認されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-187">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="f025e-188">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-188">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="f025e-189">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f025e-189">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="f025e-190">エンドポイント ルーティング ミドルウェア (<xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A> を含む <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>) により、Razor Pages エンドポイントが要求パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-190">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="f025e-191">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-191">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="f025e-192">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> です。</span><span class="sxs-lookup"><span data-stu-id="f025e-192"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="f025e-193">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-193">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="f025e-194">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-194">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="f025e-195">静的ファイル ミドルウェアでは、承認チェックは提供 **されません**。</span><span class="sxs-lookup"><span data-stu-id="f025e-195">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="f025e-196">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-196">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="f025e-197">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f025e-197">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="f025e-198">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-198">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="f025e-199">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="f025e-199">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="f025e-200">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="f025e-200">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="f025e-201">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="f025e-201">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="f025e-202">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="f025e-202">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="f025e-203">Razor Pages の応答は圧縮できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-203">The Razor Pages responses can be compressed.</span></span>

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

<span data-ttu-id="f025e-204">シングルページ アプリケーション (SPA) の場合、通常はミドルウェア パイプラインの最後に SPA ミドルウェア <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> を配置します。</span><span class="sxs-lookup"><span data-stu-id="f025e-204">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="f025e-205">SPA ミドルウェアは、次のために最後になります。</span><span class="sxs-lookup"><span data-stu-id="f025e-205">The SPA middleware comes last:</span></span>

* <span data-ttu-id="f025e-206">対応する要求に最初にその他のミドルウェアが応答するため。</span><span class="sxs-lookup"><span data-stu-id="f025e-206">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="f025e-207">クライアント側ルーティングを使用する SPA がサーバー アプリに認識されないすべてのルートを実行するため。</span><span class="sxs-lookup"><span data-stu-id="f025e-207">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="f025e-208">SPA の詳細については、[React](xref:spa/react) と [Angular](xref:spa/angular) プロジェクト テンプレートのガイドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f025e-208">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="f025e-209">Forwarded Headers Middleware の順序</span><span class="sxs-lookup"><span data-stu-id="f025e-209">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="f025e-210">ミドルウェア パイプラインを分岐する</span><span class="sxs-lookup"><span data-stu-id="f025e-210">Branch the middleware pipeline</span></span>

<span data-ttu-id="f025e-211"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="f025e-211"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="f025e-212">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="f025e-212">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="f025e-213">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-213">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="f025e-214">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="f025e-214">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="f025e-215">要求</span><span class="sxs-lookup"><span data-stu-id="f025e-215">Request</span></span>             | <span data-ttu-id="f025e-216">応答</span><span class="sxs-lookup"><span data-stu-id="f025e-216">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="f025e-217">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="f025e-217">localhost:1234</span></span>      | <span data-ttu-id="f025e-218">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="f025e-218">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="f025e-219">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="f025e-219">localhost:1234/map1</span></span> | <span data-ttu-id="f025e-220">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="f025e-220">Map Test 1</span></span>                   |
| <span data-ttu-id="f025e-221">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="f025e-221">localhost:1234/map2</span></span> | <span data-ttu-id="f025e-222">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="f025e-222">Map Test 2</span></span>                   |
| <span data-ttu-id="f025e-223">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="f025e-223">localhost:1234/map3</span></span> | <span data-ttu-id="f025e-224">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="f025e-224">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="f025e-225">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-225">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="f025e-226">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="f025e-226">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="f025e-227">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-227">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="f025e-228"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="f025e-228"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="f025e-229">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="f025e-229">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="f025e-230">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-230">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="f025e-231">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="f025e-231">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="f025e-232">要求</span><span class="sxs-lookup"><span data-stu-id="f025e-232">Request</span></span>                       | <span data-ttu-id="f025e-233">応答</span><span class="sxs-lookup"><span data-stu-id="f025e-233">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="f025e-234">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="f025e-234">localhost:1234</span></span>                | <span data-ttu-id="f025e-235">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="f025e-235">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="f025e-236">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="f025e-236">localhost:1234/?branch=master</span></span> | <span data-ttu-id="f025e-237">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="f025e-237">Branch used = master</span></span>         |

<span data-ttu-id="f025e-238">また <xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> では、指定された述語の結果に基づいて、要求パイプラインが分岐されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-238"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="f025e-239">`MapWhen` とは異なり、この分岐は、ショートしたり、ターミナル ミドルウェアが含まれたりしなければ、メイン パイプラインに再参加します。</span><span class="sxs-lookup"><span data-stu-id="f025e-239">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="f025e-240">前の例では、"Hello from main pipeline." の応答が</span><span class="sxs-lookup"><span data-stu-id="f025e-240">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="f025e-241">すべての要求に対して書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="f025e-241">is written for all requests.</span></span> <span data-ttu-id="f025e-242">要求にクエリ文字列変数 `branch` が含まれる場合、メイン パイプラインの再参加前にその値がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-242">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="f025e-243">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f025e-243">Built-in middleware</span></span>

<span data-ttu-id="f025e-244">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="f025e-244">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="f025e-245">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-245">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="f025e-246">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f025e-246">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="f025e-247">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f025e-247">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="f025e-248">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f025e-248">Middleware</span></span> | <span data-ttu-id="f025e-249">説明</span><span class="sxs-lookup"><span data-stu-id="f025e-249">Description</span></span> | <span data-ttu-id="f025e-250">順番</span><span class="sxs-lookup"><span data-stu-id="f025e-250">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="f025e-251">認証</span><span class="sxs-lookup"><span data-stu-id="f025e-251">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="f025e-252">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-252">Provides authentication support.</span></span> | <span data-ttu-id="f025e-253">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="f025e-253">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="f025e-254">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-254">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="f025e-255">承認</span><span class="sxs-lookup"><span data-stu-id="f025e-255">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="f025e-256">承認のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-256">Provides authorization support.</span></span> | <span data-ttu-id="f025e-257">認証ミドルウェアの直後。</span><span class="sxs-lookup"><span data-stu-id="f025e-257">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="f025e-258">Cookie ポリシー</span><span class="sxs-lookup"><span data-stu-id="f025e-258">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="f025e-259">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="f025e-259">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="f025e-260">cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-260">Before middleware that issues cookies.</span></span> <span data-ttu-id="f025e-261">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="f025e-261">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="f025e-262">CORS</span><span class="sxs-lookup"><span data-stu-id="f025e-262">CORS</span></span>](xref:security/cors) | <span data-ttu-id="f025e-263">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="f025e-263">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="f025e-264">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-264">Before components that use CORS.</span></span> <span data-ttu-id="f025e-265">現時点では、[こちらのバグ](https://github.com/dotnet/aspnetcore/issues/23218)のため、`UseResponseCaching` の前に `UseCors` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-265">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="f025e-266">診断</span><span class="sxs-lookup"><span data-stu-id="f025e-266">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="f025e-267">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="f025e-267">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="f025e-268">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-268">Before components that generate errors.</span></span> <span data-ttu-id="f025e-269">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-269">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="f025e-270">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="f025e-270">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="f025e-271">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="f025e-271">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="f025e-272">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-272">Before components that consume the updated fields.</span></span> <span data-ttu-id="f025e-273">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="f025e-273">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="f025e-274">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="f025e-274">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="f025e-275">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="f025e-275">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="f025e-276">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-276">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="f025e-277">ヘッダーの伝達</span><span class="sxs-lookup"><span data-stu-id="f025e-277">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="f025e-278">HTTP ヘッダーを受信要求から送信 HTTP クライアント要求に伝達します。</span><span class="sxs-lookup"><span data-stu-id="f025e-278">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="f025e-279">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="f025e-279">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="f025e-280">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="f025e-280">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="f025e-281">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-281">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="f025e-282">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="f025e-282">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="f025e-283">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="f025e-283">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="f025e-284">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-284">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="f025e-285">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="f025e-285">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="f025e-286">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="f025e-286">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="f025e-287">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="f025e-287">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="f025e-288">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="f025e-288">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="f025e-289">MVC</span><span class="sxs-lookup"><span data-stu-id="f025e-289">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="f025e-290">MVC または Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="f025e-290">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="f025e-291">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-291">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="f025e-292">OWIN</span><span class="sxs-lookup"><span data-stu-id="f025e-292">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="f025e-293">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="f025e-293">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="f025e-294">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-294">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="f025e-295">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f025e-295">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="f025e-296">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-296">Provides support for caching responses.</span></span> | <span data-ttu-id="f025e-297">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-297">Before components that require caching.</span></span> <span data-ttu-id="f025e-298">`UseResponseCaching` の前に `UseCORS` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-298">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="f025e-299">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="f025e-299">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="f025e-300">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-300">Provides support for compressing responses.</span></span> | <span data-ttu-id="f025e-301">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-301">Before components that require compression.</span></span> |
| [<span data-ttu-id="f025e-302">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="f025e-302">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="f025e-303">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-303">Provides localization support.</span></span> | <span data-ttu-id="f025e-304">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-304">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="f025e-305">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="f025e-305">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="f025e-306">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="f025e-306">Defines and constrains request routes.</span></span> | <span data-ttu-id="f025e-307">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-307">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="f025e-308">SPA</span><span class="sxs-lookup"><span data-stu-id="f025e-308">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="f025e-309">シングルページ アプリケーション (SPA) の既定のページを返し、ミドルウェア チェーン内のこの時点以降のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="f025e-309">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="f025e-310">チェーンの終わりで、静的ファイルや MVC アクションなどにサービスを提供する他のミドルウェアが優先されるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="f025e-310">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="f025e-311">セッション</span><span class="sxs-lookup"><span data-stu-id="f025e-311">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="f025e-312">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-312">Provides support for managing user sessions.</span></span> | <span data-ttu-id="f025e-313">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-313">Before components that require Session.</span></span> | 
| [<span data-ttu-id="f025e-314">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="f025e-314">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="f025e-315">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-315">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="f025e-316">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-316">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="f025e-317">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="f025e-317">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="f025e-318">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-318">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="f025e-319">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-319">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="f025e-320">WebSocket</span><span class="sxs-lookup"><span data-stu-id="f025e-320">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="f025e-321">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="f025e-321">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="f025e-322">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-322">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="f025e-323">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f025e-323">Additional resources</span></span>

* <span data-ttu-id="f025e-324">[有効期間と登録のオプション](xref:fundamentals/dependency-injection#lifetime-and-registration-options)には、*スコープ*、*一時*、*シングルトン* 有効期間サービスを含むミドルウェアの完全なサンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f025e-324">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped*, *transient*, and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f025e-325">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f025e-325">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f025e-326">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="f025e-326">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="f025e-327">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="f025e-327">Each component:</span></span>

* <span data-ttu-id="f025e-328">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="f025e-328">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="f025e-329">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-329">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="f025e-330">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-330">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="f025e-331">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="f025e-331">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="f025e-332">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-332">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="f025e-333">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-333">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="f025e-334">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f025e-334">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="f025e-335">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="f025e-335">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="f025e-336">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="f025e-336">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="f025e-337"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-337"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="f025e-338">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="f025e-338">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="f025e-339">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-339">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="f025e-340">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="f025e-340">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="f025e-341">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="f025e-341">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="f025e-345">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-345">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="f025e-346">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-346">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="f025e-347">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="f025e-347">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="f025e-348">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="f025e-348">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="f025e-349">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-349">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="f025e-350">最初の <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> デリゲートが、パイプラインを終了します。</span><span class="sxs-lookup"><span data-stu-id="f025e-350">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="f025e-351">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="f025e-351">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="f025e-352">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="f025e-352">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="f025e-353">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-353">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="f025e-354">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-354">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="f025e-355">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f025e-355">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="f025e-356">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="f025e-356">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="f025e-357">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-357">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="f025e-358">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-358">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="f025e-359">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f025e-359">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="f025e-360">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="f025e-360">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="f025e-361">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-361">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="f025e-362">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-362">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="f025e-363">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f025e-363">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="f025e-364">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-364">May cause a protocol violation.</span></span> <span data-ttu-id="f025e-365">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="f025e-365">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="f025e-366">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-366">May corrupt the body format.</span></span> <span data-ttu-id="f025e-367">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="f025e-367">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="f025e-368"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="f025e-368"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="f025e-369">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="f025e-369">Middleware order</span></span>

<span data-ttu-id="f025e-370">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="f025e-370">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="f025e-371">この順序は、セキュリティ、パフォーマンス、および機能にとって **重要** です。</span><span class="sxs-lookup"><span data-stu-id="f025e-371">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="f025e-372">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-372">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="f025e-373">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="f025e-373">In the preceding code:</span></span>

* <span data-ttu-id="f025e-374">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="f025e-374">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="f025e-375">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-375">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="f025e-376">たとえば、`UseCors` と `UseAuthentication` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f025e-376">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="f025e-377">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-377">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="f025e-378">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="f025e-378">Exception/error handling</span></span>
   * <span data-ttu-id="f025e-379">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="f025e-379">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="f025e-380">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-380">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="f025e-381">データベース エラー ページ ミドルウェア (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) によりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-381">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="f025e-382">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="f025e-382">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="f025e-383">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-383">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="f025e-384">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-384">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="f025e-385">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-385">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="f025e-386">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-386">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="f025e-387">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="f025e-387">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="f025e-388">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-388">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="f025e-389">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-389">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="f025e-390">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f025e-390">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="f025e-391">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) を使い、要求パイプラインに MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="f025e-391">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="f025e-392">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-392">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="f025e-393">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> です。</span><span class="sxs-lookup"><span data-stu-id="f025e-393"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="f025e-394">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="f025e-394">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="f025e-395">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-395">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="f025e-396">静的ファイル ミドルウェアでは、承認チェックは提供 **されません**。</span><span class="sxs-lookup"><span data-stu-id="f025e-396">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="f025e-397">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-397">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="f025e-398">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f025e-398">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="f025e-399">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-399">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="f025e-400">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="f025e-400">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="f025e-401">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="f025e-401">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="f025e-402">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="f025e-402">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="f025e-403">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="f025e-403">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="f025e-404"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> からの MVC 応答を圧縮することができます。</span><span class="sxs-lookup"><span data-stu-id="f025e-404">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="f025e-405">Use、Run、および Map</span><span class="sxs-lookup"><span data-stu-id="f025e-405">Use, Run, and Map</span></span>

<span data-ttu-id="f025e-406">HTTP パイプラインを構成するには、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> を使います。</span><span class="sxs-lookup"><span data-stu-id="f025e-406">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="f025e-407">`Use` メソッドは、パイプラインをショートさせることができます (つまり `next` 要求デリゲートを呼び出さない場合)。</span><span class="sxs-lookup"><span data-stu-id="f025e-407">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="f025e-408">`Run` は規則であり、一部のミドルウェア コンポーネントは、パイプラインの最後に実行される `Run[Middleware]` メソッドを公開することがあります。</span><span class="sxs-lookup"><span data-stu-id="f025e-408">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="f025e-409"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="f025e-409"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="f025e-410">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="f025e-410">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="f025e-411">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-411">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="f025e-412">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="f025e-412">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="f025e-413">要求</span><span class="sxs-lookup"><span data-stu-id="f025e-413">Request</span></span>             | <span data-ttu-id="f025e-414">応答</span><span class="sxs-lookup"><span data-stu-id="f025e-414">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="f025e-415">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="f025e-415">localhost:1234</span></span>      | <span data-ttu-id="f025e-416">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="f025e-416">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="f025e-417">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="f025e-417">localhost:1234/map1</span></span> | <span data-ttu-id="f025e-418">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="f025e-418">Map Test 1</span></span>                   |
| <span data-ttu-id="f025e-419">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="f025e-419">localhost:1234/map2</span></span> | <span data-ttu-id="f025e-420">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="f025e-420">Map Test 2</span></span>                   |
| <span data-ttu-id="f025e-421">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="f025e-421">localhost:1234/map3</span></span> | <span data-ttu-id="f025e-422">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="f025e-422">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="f025e-423">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-423">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="f025e-424"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="f025e-424"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="f025e-425">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="f025e-425">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="f025e-426">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f025e-426">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="f025e-427">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="f025e-427">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="f025e-428">要求</span><span class="sxs-lookup"><span data-stu-id="f025e-428">Request</span></span>                       | <span data-ttu-id="f025e-429">応答</span><span class="sxs-lookup"><span data-stu-id="f025e-429">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="f025e-430">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="f025e-430">localhost:1234</span></span>                | <span data-ttu-id="f025e-431">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="f025e-431">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="f025e-432">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="f025e-432">localhost:1234/?branch=master</span></span> | <span data-ttu-id="f025e-433">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="f025e-433">Branch used = master</span></span>         |

<span data-ttu-id="f025e-434">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="f025e-434">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="f025e-435">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="f025e-435">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="f025e-436">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f025e-436">Built-in middleware</span></span>

<span data-ttu-id="f025e-437">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="f025e-437">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="f025e-438">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="f025e-438">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="f025e-439">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f025e-439">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="f025e-440">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f025e-440">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="f025e-441">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f025e-441">Middleware</span></span> | <span data-ttu-id="f025e-442">説明</span><span class="sxs-lookup"><span data-stu-id="f025e-442">Description</span></span> | <span data-ttu-id="f025e-443">順番</span><span class="sxs-lookup"><span data-stu-id="f025e-443">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="f025e-444">認証</span><span class="sxs-lookup"><span data-stu-id="f025e-444">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="f025e-445">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-445">Provides authentication support.</span></span> | <span data-ttu-id="f025e-446">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="f025e-446">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="f025e-447">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-447">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="f025e-448">Cookie ポリシー</span><span class="sxs-lookup"><span data-stu-id="f025e-448">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="f025e-449">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="f025e-449">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="f025e-450">cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-450">Before middleware that issues cookies.</span></span> <span data-ttu-id="f025e-451">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="f025e-451">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="f025e-452">CORS</span><span class="sxs-lookup"><span data-stu-id="f025e-452">CORS</span></span>](xref:security/cors) | <span data-ttu-id="f025e-453">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="f025e-453">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="f025e-454">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-454">Before components that use CORS.</span></span> |
| [<span data-ttu-id="f025e-455">診断</span><span class="sxs-lookup"><span data-stu-id="f025e-455">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="f025e-456">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="f025e-456">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="f025e-457">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-457">Before components that generate errors.</span></span> <span data-ttu-id="f025e-458">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-458">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="f025e-459">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="f025e-459">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="f025e-460">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="f025e-460">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="f025e-461">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-461">Before components that consume the updated fields.</span></span> <span data-ttu-id="f025e-462">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="f025e-462">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="f025e-463">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="f025e-463">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="f025e-464">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="f025e-464">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="f025e-465">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-465">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="f025e-466">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="f025e-466">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="f025e-467">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="f025e-467">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="f025e-468">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-468">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="f025e-469">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="f025e-469">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="f025e-470">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="f025e-470">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="f025e-471">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-471">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="f025e-472">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="f025e-472">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="f025e-473">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="f025e-473">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="f025e-474">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="f025e-474">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="f025e-475">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="f025e-475">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="f025e-476">MVC</span><span class="sxs-lookup"><span data-stu-id="f025e-476">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="f025e-477">MVC または Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="f025e-477">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="f025e-478">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-478">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="f025e-479">OWIN</span><span class="sxs-lookup"><span data-stu-id="f025e-479">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="f025e-480">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="f025e-480">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="f025e-481">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-481">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="f025e-482">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f025e-482">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="f025e-483">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-483">Provides support for caching responses.</span></span> | <span data-ttu-id="f025e-484">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-484">Before components that require caching.</span></span> |
| [<span data-ttu-id="f025e-485">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="f025e-485">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="f025e-486">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-486">Provides support for compressing responses.</span></span> | <span data-ttu-id="f025e-487">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-487">Before components that require compression.</span></span> |
| [<span data-ttu-id="f025e-488">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="f025e-488">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="f025e-489">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-489">Provides localization support.</span></span> | <span data-ttu-id="f025e-490">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-490">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="f025e-491">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="f025e-491">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="f025e-492">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="f025e-492">Defines and constrains request routes.</span></span> | <span data-ttu-id="f025e-493">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-493">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="f025e-494">セッション</span><span class="sxs-lookup"><span data-stu-id="f025e-494">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="f025e-495">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-495">Provides support for managing user sessions.</span></span> | <span data-ttu-id="f025e-496">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-496">Before components that require Session.</span></span> |
| [<span data-ttu-id="f025e-497">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="f025e-497">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="f025e-498">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-498">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="f025e-499">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="f025e-499">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="f025e-500">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="f025e-500">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="f025e-501">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="f025e-501">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="f025e-502">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-502">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="f025e-503">WebSocket</span><span class="sxs-lookup"><span data-stu-id="f025e-503">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="f025e-504">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="f025e-504">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="f025e-505">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="f025e-505">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="f025e-506">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f025e-506">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
