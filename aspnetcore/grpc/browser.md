---
title: ブラウザー アプリでの gRPC の使用
author: jamesnk
description: ASP.NET Core で、GRPC-Web を使用してブラウザー アプリから呼び出しできるように、gRPC サービスを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 6f66a94b41e6e13550396e2e19fdf48f9dc63d46
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106599"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="17bd8-103">ブラウザー アプリでの gRPC の使用</span><span class="sxs-lookup"><span data-stu-id="17bd8-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="17bd8-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="17bd8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="17bd8-105">ブラウザーベースのアプリから HTTP/2 gRPC サービスを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="17bd8-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="17bd8-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) は、ブラウザーの JavaScript および Blazor アプリで gRPC サービスを呼び出せるようにするプロトコルです。</span><span class="sxs-lookup"><span data-stu-id="17bd8-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="17bd8-107">この記事では、.NET Core で gRPC-Web を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="17bd8-108">ASP.NET Core の gRPC-Web と Envoy</span><span class="sxs-lookup"><span data-stu-id="17bd8-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="17bd8-109">gRPC-Web を ASP.NET Core アプリに追加する方法には、次の 2 つの選択肢があります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="17bd8-110">ASP.NET Core の gRPC HTTP/2 と共に、gRPC-Web をサポートします。</span><span class="sxs-lookup"><span data-stu-id="17bd8-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="17bd8-111">このオプションでは、`Grpc.AspNetCore.Web` パッケージによって提供されるミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="17bd8-112">[Envoy プロキシ](https://www.envoyproxy.io/)の gRPC-Web サポートを使用して、gRPC-Web を gRPC HTTP/2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="17bd8-113">変換された呼び出しが、ASP.NET Core アプリに転送されます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="17bd8-114">それぞれの方法には、長所と短所があります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="17bd8-115">アプリの環境で既にプロキシとして Envoy を使用している場合は、gRPC-Web サポートを提供するためにこれを使用するのも、妥当かもしれません。</span><span class="sxs-lookup"><span data-stu-id="17bd8-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="17bd8-116">ASP.NET Core のみを必要とする gRPC-Web の単純なソリューションが必要な場合は、`Grpc.AspNetCore.Web` を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="17bd8-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="17bd8-117">ASP.NET Core での gRPC-Web の構成</span><span class="sxs-lookup"><span data-stu-id="17bd8-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="17bd8-118">ASP.NET Core でホストされている gRPC サービスは、HTTP/2 gRPC と共に gRPC-Web をサポートするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="17bd8-119">gRPC-Web では、サービスを変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="17bd8-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="17bd8-120">唯一の変更点はスタートアップ構成です。</span><span class="sxs-lookup"><span data-stu-id="17bd8-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="17bd8-121">ASP.NET Core gRPC サービスで gRPC-Web を有効にするには:</span><span class="sxs-lookup"><span data-stu-id="17bd8-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="17bd8-122">[Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="17bd8-123">アプリで gRPC-Web を使用するように構成するには、`UseGrpcWeb` と `EnableGrpcWeb` を *Startup.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="17bd8-124">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-124">The preceding code:</span></span>

* <span data-ttu-id="17bd8-125">GRPC-Web ミドルウェアの `UseGrpcWeb` を、ルーティングの後かつエンドポイントの前に追加します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="17bd8-126">`endpoints.MapGrpcService<GreeterService>()` メソッドで、`EnableGrpcWeb` を使用して gRPC-Web をサポートすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="17bd8-127">または、gRPC-Web ミドルウェアを構成して、すべてのサービスで既定で gRPC-Web がサポートされ、`EnableGrpcWeb` が不要になるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="17bd8-128">ミドルウェアを追加するときに `new GrpcWebOptions { DefaultEnabled = true }` を指定します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="17bd8-129">.NET Core 3.x で [Http.sys によりホストされている](xref:fundamentals/servers/httpsys)場合は、gRPC-Web が失敗する原因となる既知の問題があります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="17bd8-130">Http.sys で gRPC-Web を機能させるには、[こちら](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)で回避策を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17bd8-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="17bd8-131">gRPC-Web と CORS</span><span class="sxs-lookup"><span data-stu-id="17bd8-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="17bd8-132">ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="17bd8-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="17bd8-133">この制限は、ブラウザー アプリで gRPC-Web 呼び出しを行う場合に適用されます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="17bd8-134">たとえば、`https://www.contoso.com` によって提供されるブラウザー アプリでは、`https://services.contoso.com` でホストされている gRPC-Web サービスの呼び出しがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="17bd8-135">この制限を緩和するには、クロス オリジン リソース共有 (CORS) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="17bd8-136">ブラウザー アプリでクロス オリジン gRPC-Web 呼び出しを行えるようにするには、[ASP.NET Core で CORS](xref:security/cors) を設定します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="17bd8-137">組み込みの CORS サポートを使用し、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> で gRPC 固有のヘッダーを公開します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="17bd8-138">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-138">The preceding code:</span></span>

* <span data-ttu-id="17bd8-139">`AddCors` を呼び出して CORS サービスを追加し、gRPC 固有のヘッダーを公開する CORS ポリシーを構成します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="17bd8-140">`UseCors` を呼び出して、ルーティングの後かつエンドポイントの前に CORS ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="17bd8-141">`endpoints.MapGrpcService<GreeterService>()` メソッドが `RequiresCors` で CORS をサポートすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="17bd8-142">ブラウザーから gRPC-Web を呼び出す</span><span class="sxs-lookup"><span data-stu-id="17bd8-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="17bd8-143">ブラウザー アプリでは gRPC-Web を使用して gRPC サービスを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="17bd8-144">ブラウザーから gRPC-Web を使用して gRPC サービスを呼び出す場合、いくつかの要件と制限があります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="17bd8-145">サーバーは、gRPC-Web をサポートするように構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="17bd8-146">クライアント ストリーミングと双方向ストリーミングの呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="17bd8-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="17bd8-147">サーバー ストリーミングはサポートされています。</span><span class="sxs-lookup"><span data-stu-id="17bd8-147">Server streaming is supported.</span></span>
* <span data-ttu-id="17bd8-148">別のドメインで gRPC サービスを呼び出すには、サーバーで [CORS](xref:security/cors) を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="17bd8-149">JavaScript gRPC-Web クライアント</span><span class="sxs-lookup"><span data-stu-id="17bd8-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="17bd8-150">JavaScript gRPC-Web クライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="17bd8-151">JavaScript から gRPC-Web を使用する方法については、[gRPC-Web を使用した JavaScript クライアント コードの作成](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="17bd8-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="17bd8-152">.NET gRPC クライアントを使用して gRPC-Web を構成する</span><span class="sxs-lookup"><span data-stu-id="17bd8-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="17bd8-153">gRPC-Web 呼び出しを行うように .NET gRPC クライアントを構成できます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="17bd8-154">これは、ブラウザーでホストされ、JavaScript コードの同じ HTTP 制限がある [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) アプリに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="17bd8-155">.NET クライアントによる gRPC-Web の呼び出しは、[HTTP/2 gRPC と同じ](xref:grpc/client)です。</span><span class="sxs-lookup"><span data-stu-id="17bd8-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="17bd8-156">唯一の変更点は、チャネルの作成方法です。</span><span class="sxs-lookup"><span data-stu-id="17bd8-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="17bd8-157">gRPC-Web を使用するには:</span><span class="sxs-lookup"><span data-stu-id="17bd8-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="17bd8-158">[Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="17bd8-159">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) パッケージへの参照が確実に 2.29.0 以上であるようにします。</span><span class="sxs-lookup"><span data-stu-id="17bd8-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="17bd8-160">`GrpcWebHandler` を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="17bd8-161">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="17bd8-161">The preceding code:</span></span>

* <span data-ttu-id="17bd8-162">gRPC-Web を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="17bd8-163">クライアントを作成し、チャネルを使用して呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="17bd8-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="17bd8-164">`GrpcWebHandler` には次の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="17bd8-165">**InnerHandler**:gRPC HTTP 要求を行う基になる <xref:System.Net.Http.HttpMessageHandler> (`HttpClientHandler` など)。</span><span class="sxs-lookup"><span data-stu-id="17bd8-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="17bd8-166">**GrpcWebMode**:gRPC HTTP 要求の `Content-Type` が `application/grpc-web` または `application/grpc-web-text` であるかどうかを指定する列挙型。</span><span class="sxs-lookup"><span data-stu-id="17bd8-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="17bd8-167">`GrpcWebMode.GrpcWeb` は、コンテンツをエンコードせずに送信するように構成します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="17bd8-168">既定値です。</span><span class="sxs-lookup"><span data-stu-id="17bd8-168">Default value.</span></span>
    * <span data-ttu-id="17bd8-169">`GrpcWebMode.GrpcWebText` は、コンテンツを base64 でエンコードするように構成します。</span><span class="sxs-lookup"><span data-stu-id="17bd8-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="17bd8-170">ブラウザーでのサーバー ストリーム呼び出しに必要です。</span><span class="sxs-lookup"><span data-stu-id="17bd8-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="17bd8-171">**HttpVersion**:基になる gRPC HTTP 要求で、[HttpRequestMessage](xref:System.Net.Http.HttpRequestMessage.Version) を設定するために使用される HTTP プロトコル `Version`。</span><span class="sxs-lookup"><span data-stu-id="17bd8-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="17bd8-172">gRPC-Web では特定のバージョンを必要とせず、指定しない限り、既定値はオーバーライドされません。</span><span class="sxs-lookup"><span data-stu-id="17bd8-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="17bd8-173">生成された gRPC クライアントには、単項メソッドを呼び出すための同期メソッドと非同期メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="17bd8-174">たとえば、`SayHello` は同期であり、`SayHelloAsync` は非同期です。</span><span class="sxs-lookup"><span data-stu-id="17bd8-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="17bd8-175">Blazor WebAssembly アプリで同期メソッドを呼び出すと、アプリが応答しなくなります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="17bd8-176">Blazor WebAssembly では、常に非同期メソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17bd8-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17bd8-177">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="17bd8-177">Additional resources</span></span>

* [<span data-ttu-id="17bd8-178">Web クライアント用 gRPC の GitHub プロジェクト</span><span class="sxs-lookup"><span data-stu-id="17bd8-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
