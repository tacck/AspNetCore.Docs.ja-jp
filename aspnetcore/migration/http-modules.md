---
title: HTTP ハンドラーとモジュールを ASP.NET Core ミドルウェアに移行する
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/http-modules
ms.openlocfilehash: 92672b2d05ee6bbdfcf0255ae14529a5c28c41b7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014985"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="5ad7e-102">HTTP ハンドラーとモジュールを ASP.NET Core ミドルウェアに移行する</span><span class="sxs-lookup"><span data-stu-id="5ad7e-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="5ad7e-103">この記事では、既存の ASP.NET [HTTP モジュールとハンドラーを system.webserver から](/iis/configuration/system.webserver/)ASP.NET Core[ミドルウェア](xref:fundamentals/middleware/index)に移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="5ad7e-104">再検討されたモジュールとハンドラー</span><span class="sxs-lookup"><span data-stu-id="5ad7e-104">Modules and handlers revisited</span></span>

<span data-ttu-id="5ad7e-105">ミドルウェアの ASP.NET Core に進む前に、まず HTTP モジュールとハンドラーがどのように機能するかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![モジュールハンドラー](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="5ad7e-107">**ハンドラーは次のとおりです。**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-107">**Handlers are:**</span></span>

* <span data-ttu-id="5ad7e-108">[IHttpHandler](/dotnet/api/system.web.ihttphandler)を実装するクラス</span><span class="sxs-lookup"><span data-stu-id="5ad7e-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="5ad7e-109">指定されたファイル名または拡張子を持つ要求を処理するために使用され*ます。レポートなどです。*</span><span class="sxs-lookup"><span data-stu-id="5ad7e-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="5ad7e-110">*Web.config*で[構成済み](/iis/configuration/system.webserver/handlers/)</span><span class="sxs-lookup"><span data-stu-id="5ad7e-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="5ad7e-111">**モジュールは次のとおりです。**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-111">**Modules are:**</span></span>

* <span data-ttu-id="5ad7e-112">[IHttpModule](/dotnet/api/system.web.ihttpmodule)を実装するクラス</span><span class="sxs-lookup"><span data-stu-id="5ad7e-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="5ad7e-113">すべての要求に対して呼び出されます</span><span class="sxs-lookup"><span data-stu-id="5ad7e-113">Invoked for every request</span></span>

* <span data-ttu-id="5ad7e-114">ショートサーキットを可能にする (要求の後続の処理を停止する)</span><span class="sxs-lookup"><span data-stu-id="5ad7e-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="5ad7e-115">HTTP 応答に追加することも、独自の応答を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="5ad7e-116">*Web.config*で[構成済み](/iis/configuration/system.webserver/modules/)</span><span class="sxs-lookup"><span data-stu-id="5ad7e-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="5ad7e-117">**受信要求を処理するモジュールの順序は、次のように決定されます。**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="5ad7e-118">[アプリケーションライフサイクル](https://msdn.microsoft.com/library/ms227673.aspx)。 ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest)、 [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)などによって起動されるシリーズイベントです。各モジュールは、1つまたは複数のイベントのハンドラーを作成できます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="5ad7e-119">同じイベントの場合、 *Web.config*で構成されている順序。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="5ad7e-120">モジュールに加えて、ライフサイクルイベントのハンドラーを*Global.asax.cs*ファイルに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="5ad7e-121">これらのハンドラーは、構成されているモジュールのハンドラーの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="5ad7e-122">ハンドラーとモジュールからミドルウェアへ</span><span class="sxs-lookup"><span data-stu-id="5ad7e-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="5ad7e-123">**ミドルウェアは、HTTP モジュールとハンドラーよりも簡単です。**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="5ad7e-124">モジュール、ハンドラー、 *Global.asax.cs*、 *Web.config* (IIS 構成を除く) とアプリケーションのライフサイクルが失われる</span><span class="sxs-lookup"><span data-stu-id="5ad7e-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="5ad7e-125">ミドルウェアによってモジュールとハンドラーの両方のロールが取得されています。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="5ad7e-126">ミドルウェアは、ではなくコードを使用して構成*Web.config*</span><span class="sxs-lookup"><span data-stu-id="5ad7e-126">Middleware are configured using code rather than in *Web.config*</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="5ad7e-127">[パイプライン分岐](xref:fundamentals/middleware/index#branch-the-middleware-pipeline)を使用すると、URL だけでなく、要求ヘッダー、クエリ文字列などに基づいて、特定のミドルウェアに要求を送信できます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-127">[Pipeline branching](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="5ad7e-128">[パイプライン分岐](xref:fundamentals/middleware/index#use-run-and-map)を使用すると、URL だけでなく、要求ヘッダー、クエリ文字列などに基づいて、特定のミドルウェアに要求を送信できます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end

<span data-ttu-id="5ad7e-129">**ミドルウェアはモジュールとよく似ています。**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="5ad7e-130">すべての要求に対して、原則として呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="5ad7e-131">要求を[次のミドルウェアに渡す](#http-modules-shortcircuiting-middleware)ことによって、要求をショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="5ad7e-132">独自の HTTP 応答を作成できる</span><span class="sxs-lookup"><span data-stu-id="5ad7e-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="5ad7e-133">**ミドルウェアとモジュールは、別の順序で処理されます。**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="5ad7e-134">ミドルウェアの順序は、要求パイプラインに挿入される順序に基づいていますが、モジュールの順序は主に[アプリケーションライフサイクル](https://msdn.microsoft.com/library/ms227673.aspx)イベントに基づいています。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="5ad7e-135">応答のミドルウェアの順序は、要求の場合と逆になりますが、モジュールの順序は要求と応答に対して同じです。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="5ad7e-136">「 [IApplicationBuilder を使用したミドルウェアパイプラインの作成](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![ミドルウェア](http-modules/_static/middleware.png)

<span data-ttu-id="5ad7e-138">上の図のように、認証ミドルウェアは要求のショートサーキットを行います。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="5ad7e-139">ミドルウェアへのモジュールコードの移行</span><span class="sxs-lookup"><span data-stu-id="5ad7e-139">Migrating module code to middleware</span></span>

<span data-ttu-id="5ad7e-140">既存の HTTP モジュールは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="5ad7e-141">[ミドルウェア](xref:fundamentals/middleware/index)ページに示されているように、ASP.NET Core ミドルウェアは、を `Invoke` 取得してを返すメソッドを公開するクラスです `HttpContext` `Task` 。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="5ad7e-142">新しいミドルウェアは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="5ad7e-143">前のミドルウェアテンプレートは、[ミドルウェアの作成](xref:fundamentals/middleware/write)に関するセクションから取得されました。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="5ad7e-144">*MyMiddlewareExtensions* helper クラスを使用すると、クラスでミドルウェアを簡単に構成 `Startup` できます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="5ad7e-145">メソッドは、 `UseMyMiddleware` 要求パイプラインにミドルウェアクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="5ad7e-146">ミドルウェアが必要とするサービスは、ミドルウェアのコンストラクターに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="5ad7e-147">ユーザーが承認されていないなど、モジュールが要求を終了する場合があります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="5ad7e-148">ミドルウェアは `Invoke` 、パイプラインの次のミドルウェアでを呼び出さずにこの処理を行います。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="5ad7e-149">これは、要求を完全に終了しないことに注意してください。これは、応答がパイプラインを通過するときに前のミドルウェアが呼び出されるためです。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="5ad7e-150">モジュールの機能を新しいミドルウェアに移行するときに、 `HttpContext` クラスが ASP.NET Core で大幅に変更されているため、コードがコンパイルされないことがあります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="5ad7e-151">[後](#migrating-to-the-new-httpcontext)で、新しい ASP.NET Core HttpContext に移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="5ad7e-152">要求パイプラインへのモジュール挿入の移行</span><span class="sxs-lookup"><span data-stu-id="5ad7e-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="5ad7e-153">HTTP モジュールは通常、 *Web.config*を使用して要求パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-153">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="5ad7e-154">クラスの要求パイプラインに[新しいミドルウェアを追加](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)して、これを変換し `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="5ad7e-155">新しいミドルウェアを挿入するパイプライン内の正確な位置は、モジュール (、など) として処理されたイベント `BeginRequest` `EndRequest` と、 *Web.config*内のモジュールの一覧での順序によって異なります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="5ad7e-156">前述のように、ASP.NET Core にはアプリケーションのライフサイクルがなく、ミドルウェアによって応答が処理される順序は、モジュールで使用される順序とは異なります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="5ad7e-157">これにより、順序を決定することが困難になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="5ad7e-158">順序付けが問題になった場合は、個別に並べ替えることのできる複数のミドルウェアコンポーネントにモジュールを分割できます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="5ad7e-159">ミドルウェアへのハンドラーコードの移行</span><span class="sxs-lookup"><span data-stu-id="5ad7e-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="5ad7e-160">HTTP ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="5ad7e-161">ASP.NET Core プロジェクトでは、これを次のようなミドルウェアに変換します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="5ad7e-162">このミドルウェアは、モジュールに対応するミドルウェアと非常によく似ています。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="5ad7e-163">実際の違いは、ここではへの呼び出しがないことです `_next.Invoke(context)` 。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="5ad7e-164">これは、ハンドラーが要求パイプラインの最後にあるため、次に呼び出すミドルウェアが存在しないため、意味があります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="5ad7e-165">要求パイプラインへのハンドラー挿入の移行</span><span class="sxs-lookup"><span data-stu-id="5ad7e-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="5ad7e-166">HTTP ハンドラーの構成は*Web.config*で行われ、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="5ad7e-167">これを変換するには、新しいハンドラーミドルウェアをクラスの要求パイプラインに追加し `Startup` ます。これは、モジュールから変換されたミドルウェアに似ています。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="5ad7e-168">この方法の問題は、すべての要求を新しいハンドラーミドルウェアに送信することです。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="5ad7e-169">ただし、特定の拡張機能を持つ要求のみがミドルウェアに到着するようにします。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="5ad7e-170">これにより、HTTP ハンドラーと同じ機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="5ad7e-171">1つの解決策は、拡張メソッドを使用して、特定の拡張機能を持つ要求に対してパイプラインを分岐することです `MapWhen` 。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="5ad7e-172">これは、もう一方のミドルウェアを追加するのと同じ方法で行い `Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="5ad7e-173">`MapWhen`次のパラメーターを取得します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="5ad7e-174">を受け取るラムダ `HttpContext` `true` 。要求が分岐を下位に移動する必要がある場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="5ad7e-175">これは、拡張機能だけでなく、要求ヘッダー、クエリ文字列パラメーターなどにもブランチ要求を分岐できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="5ad7e-176">を受け取り、 `IApplicationBuilder` 分岐のすべてのミドルウェアを追加するラムダ。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="5ad7e-177">これは、ハンドラーミドルウェアの前に分岐にミドルウェアを追加できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="5ad7e-178">ブランチがすべての要求で呼び出される前に、ミドルウェアがパイプラインに追加されました。ブランチには影響がありません。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="5ad7e-179">オプションパターンを使用したミドルウェアオプションの読み込み</span><span class="sxs-lookup"><span data-stu-id="5ad7e-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="5ad7e-180">一部のモジュールとハンドラーには、 *Web.config*に格納されている構成オプションがあります。ただし ASP.NET Core では、 *Web.config*の代わりに新しい構成モデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="5ad7e-181">新しい[構成システム](xref:fundamentals/configuration/index)には、これを解決するための次のオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="5ad7e-182">[次のセクション](#loading-middleware-options-through-direct-injection)で示すように、ミドルウェアにオプションを直接挿入します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="5ad7e-183">オプションの[パターン](xref:fundamentals/configuration/options)を使用します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="5ad7e-184">ミドルウェアオプションを保持するクラスを作成します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="5ad7e-185">オプションの値を格納する</span><span class="sxs-lookup"><span data-stu-id="5ad7e-185">Store the option values</span></span>

   <span data-ttu-id="5ad7e-186">構成システムでは、オプションの値を任意の場所に格納できます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="5ad7e-187">ただし、ほとんどのサイトでは*appsettings.js*が使用されているので、次の方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-187">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="5ad7e-188">*MyMiddlewareOptionsSection*ここにはセクション名があります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="5ad7e-189">オプションクラスの名前と同じである必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="5ad7e-190">オプションの値を options クラスに関連付ける</span><span class="sxs-lookup"><span data-stu-id="5ad7e-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="5ad7e-191">オプションのパターンでは ASP.NET Core の依存関係挿入フレームワークを使用して、オプションの種類 (など `MyMiddlewareOptions` ) を `MyMiddlewareOptions` 実際のオプションを持つオブジェクトに関連付けます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="5ad7e-192">クラスを更新し `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="5ad7e-193">*でappsettings.js*を使用している場合は、コンストラクターの構成ビルダーに追加し `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-193">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="5ad7e-194">オプションサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="5ad7e-195">オプションをオプションクラスに関連付けます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="5ad7e-196">ミドルウェアコンストラクターにオプションを挿入します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="5ad7e-197">これは、コントローラーにオプションを挿入するのと似ています。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="5ad7e-198">にミドルウェアを追加する[UseMiddleware](#http-modules-usemiddleware)拡張メソッドは、 `IApplicationBuilder` 依存関係の挿入を行います。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="5ad7e-199">これはオブジェクトに限定されません `IOptions` 。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="5ad7e-200">ミドルウェアが必要とするその他のオブジェクトは、この方法で挿入できます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="5ad7e-201">直接挿入によるミドルウェアオプションの読み込み</span><span class="sxs-lookup"><span data-stu-id="5ad7e-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="5ad7e-202">オプションのパターンには、オプションの値とコンシューマーの間に疎結合が作成されるという利点があります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="5ad7e-203">オプションクラスを実際のオプション値に関連付けた後、他のクラスは、依存関係挿入フレームワークを通じてオプションにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="5ad7e-204">オプションの値を渡す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="5ad7e-205">これは、異なるオプションを使用して同じミドルウェアを2回使用する場合には、このようになります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="5ad7e-206">たとえば、異なるブランチで使用される承認ミドルウェアは、さまざまなロールを許可します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="5ad7e-207">2つの異なる options オブジェクトを1つの options クラスに関連付けることはできません。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="5ad7e-208">ソリューションでは、オプションオブジェクトをクラスの実際のオプション値で取得 `Startup` し、ミドルウェアの各インスタンスに直接渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="5ad7e-209">*appsettings.js*に2番目のキーを追加する</span><span class="sxs-lookup"><span data-stu-id="5ad7e-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="5ad7e-210">ファイルの*appsettings.js*に2番目のオプションセットを追加するには、新しいキーを使用して、それを一意に識別します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="5ad7e-211">オプションの値を取得し、ミドルウェアに渡します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="5ad7e-212">`Use...`(ミドルウェアをパイプラインに追加する) 拡張メソッドは、オプション値を渡す論理的な場所です。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="5ad7e-213">ミドルウェアがオプションパラメーターを受け取ることができるようにします。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="5ad7e-214">`Use...`拡張メソッド (options パラメーターを受け取り、に渡す) のオーバーロードを指定し `UseMiddleware` ます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="5ad7e-215">パラメーターを使用して `UseMiddleware` を呼び出すと、ミドルウェアオブジェクトをインスタンス化するときに、ミドルウェアコンストラクターにパラメーターが渡されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="5ad7e-216">これにより、オブジェクトの options オブジェクトがどのようにラップされるかに注目 `OptionsWrapper` してください。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="5ad7e-217">これは `IOptions` 、ミドルウェアコンストラクターによって想定されているとおりにを実装します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="5ad7e-218">新しい HttpContext への移行</span><span class="sxs-lookup"><span data-stu-id="5ad7e-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="5ad7e-219">前 `Invoke` に、ミドルウェア内のメソッドが型のパラメーターを受け取ることを確認しました `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="5ad7e-220">`HttpContext`は ASP.NET Core で大幅に変更されました。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="5ad7e-221">このセクションでは、 [system.web](/dotnet/api/system.web.httpcontext)の最もよく使用されるプロパティを新しいに変換する方法について説明します `Microsoft.AspNetCore.Http.HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="5ad7e-222">Httpcontext.current</span><span class="sxs-lookup"><span data-stu-id="5ad7e-222">HttpContext</span></span>

<span data-ttu-id="5ad7e-223">HttpContext は次のように変換さ**れます。**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="5ad7e-224">**一意の要求 ID (対応する System.web)**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="5ad7e-225">要求ごとに一意の id を提供します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="5ad7e-226">ログに含めるのに非常に便利です。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="5ad7e-227">HttpContext. 要求</span><span class="sxs-lookup"><span data-stu-id="5ad7e-227">HttpContext.Request</span></span>

<span data-ttu-id="5ad7e-228">**HttpMethod**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="5ad7e-229">**Httpcontext.current**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="5ad7e-230">**Httpcontext. url**と**httpcontext.current url**はに変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="5ad7e-231">**HttpContext. IsSecureConnection**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="5ad7e-232">**HttpContext. UserHostAddress**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="5ad7e-233">**HttpContext. 要求。 Cookies**は次のように変換します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="5ad7e-234">**Httpcontext.current**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="5ad7e-235">**HttpContext。ヘッダー**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="5ad7e-236">**UserAgent**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="5ad7e-237">**Httpcontext.current**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="5ad7e-238">**HttpContext。 ContentType**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="5ad7e-239">**HttpContext。フォーム**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="5ad7e-240">コンテンツサブタイプが*url エンコード*または*フォームデータ*の場合にのみ、フォーム値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="5ad7e-241">**InputStream**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="5ad7e-242">このコードは、パイプラインの最後のハンドラー型ミドルウェアでのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="5ad7e-243">前に示したように、未加工の本文は、要求ごとに1回だけ読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="5ad7e-244">最初の読み取り後に、ミドルウェアが本文を読み取ろうとすると、空の本文が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="5ad7e-245">これは、前に示したように、フォームの読み取りには適用されません。これは、バッファーから実行されるためです。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="5ad7e-246">HttpContext</span><span class="sxs-lookup"><span data-stu-id="5ad7e-246">HttpContext.Response</span></span>

<span data-ttu-id="5ad7e-247">**Httpcontext.current**は、次のように変換されます。 **statusdescription** :</span><span class="sxs-lookup"><span data-stu-id="5ad7e-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="5ad7e-248">**Httpcontext.current**は、次のように**変換します。**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="5ad7e-249">また、その独自の**ContentType**にも、次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="5ad7e-250">**出力**は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="5ad7e-251">**TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="5ad7e-252">ファイルの提供については、[こちら](../fundamentals/request-features.md#middleware-and-request-features)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-252">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="5ad7e-253">**HttpContext. 応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="5ad7e-254">応答ヘッダーの送信は、応答本文に何かが書き込まれた後に設定した場合、送信されないという事実により複雑になります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="5ad7e-255">解決策として、応答の書き込みが開始される前に、右に呼び出されるコールバックメソッドを設定します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="5ad7e-256">これは、ミドルウェアのメソッドの開始時に実行することをお勧め `Invoke` します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="5ad7e-257">応答ヘッダーを設定するコールバックメソッドです。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="5ad7e-258">次のコードは、というコールバックメソッドを設定し `SetHeaders` ます。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="5ad7e-259">`SetHeaders`コールバックメソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="5ad7e-260">**CookieHttpContext。2$s**</span><span class="sxs-lookup"><span data-stu-id="5ad7e-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="5ad7e-261">Cookieは、\*セット Cookie \*応答ヘッダー内のブラウザーに移動します。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="5ad7e-262">そのため、を送信するには、 cookie 応答ヘッダーの送信に使用するのと同じコールバックが必要です。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="5ad7e-263">`SetCookies`コールバックメソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5ad7e-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="5ad7e-264">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="5ad7e-264">Additional resources</span></span>

* [<span data-ttu-id="5ad7e-265">HTTP ハンドラーと HTTP モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="5ad7e-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="5ad7e-266">構成</span><span class="sxs-lookup"><span data-stu-id="5ad7e-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="5ad7e-267">アプリケーションの起動</span><span class="sxs-lookup"><span data-stu-id="5ad7e-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="5ad7e-268">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="5ad7e-268">Middleware</span></span>](xref:fundamentals/middleware/index)
