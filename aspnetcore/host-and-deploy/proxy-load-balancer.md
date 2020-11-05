---
title: プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する
author: rick-anderson
description: プロキシ サーバーとロード バランサーの背後にホストされているアプリの構成について説明します。このような構成では、要求の重要な情報がわからなくなることがよくあります。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 461f6d2105d38c5dbea2f8cf479e027c2edede14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057623"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="bcfda-103">プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する</span><span class="sxs-lookup"><span data-stu-id="bcfda-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="bcfda-104">作成者: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="bcfda-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bcfda-105">ASP.NET Core の推奨される構成では、アプリは IIS/ASP.NET Core モジュール、Nginx、または Apache を使ってホストされます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="bcfda-106">プロキシ サーバー、ロード バランサー、および他のネットワーク アプライアンスにより、要求に関する情報が、アプリに到達する前にわからなくなることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="bcfda-107">HTTPS 要求が HTTP によってプロキシされると、元のスキーム (HTTPS) は失われ、ヘッダーで転送される必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="bcfda-108">アプリは、インターネット上または社内ネットワーク上の本来の送信元ではなく、プロキシから要求を受信するため、送信元クライアントの IP アドレスもヘッダーで転送される必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="bcfda-109">この情報は、リダイレクト、認証、リンクの生成、ポリシーの評価、クライアントの位置情報など、要求の処理で重要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="bcfda-110">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="bcfda-110">Forwarded headers</span></span>

<span data-ttu-id="bcfda-111">慣例によりは、プロキシは HTTP ヘッダーで情報を転送します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="bcfda-112">Header</span><span class="sxs-lookup"><span data-stu-id="bcfda-112">Header</span></span> | <span data-ttu-id="bcfda-113">説明</span><span class="sxs-lookup"><span data-stu-id="bcfda-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="bcfda-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="bcfda-114">X-Forwarded-For</span></span> | <span data-ttu-id="bcfda-115">要求を開始したクライアントと、プロキシ チェーン内の後続のプロキシに関する情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="bcfda-116">このパラメーターは、IP アドレス (および、必要に応じてポート番号) を含むことがあります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="bcfda-117">プロキシ サーバーのチェーンにおいては、最初のパラメーターが、要求を最初に行ったクライアントを示します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="bcfda-118">その後に、後続のプロキシの識別子が指定されています。</span><span class="sxs-lookup"><span data-stu-id="bcfda-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="bcfda-119">チェーン内の最後のプロキシは、パラメーターのリストには含まれません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="bcfda-120">最後のプロキシの IP アドレスとオプションのポート番号は、トランスポート層のリモート IP アドレスとして入手できます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="bcfda-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="bcfda-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="bcfda-122">開始スキーム (HTTP/HTTPS) の値です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="bcfda-123">要求が複数のプロキシを通過している場合、値はスキームのリストである場合もあります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="bcfda-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="bcfda-124">X-Forwarded-Host</span></span> | <span data-ttu-id="bcfda-125">ホスト ヘッダー フィールドの元の値です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-125">The original value of the Host header field.</span></span> <span data-ttu-id="bcfda-126">通常、プロキシはホスト ヘッダーを変更しません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="bcfda-127">プロキシにおいてホスト ヘッダーが既知の適切な値であることが検証されない、または既知の適切な値に制限されないシステムに影響を与える、特権の昇格脆弱性については、[マイクロソフト セキュリティ アドバイザリ CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="bcfda-128">Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>) によって、これらのヘッダーが読み取られ、<xref:Microsoft.AspNetCore.Http.HttpContext> 上の関連するフィールドに入力されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="bcfda-129">ミドルウェアの更新:</span><span class="sxs-lookup"><span data-stu-id="bcfda-129">The middleware updates:</span></span>

* <span data-ttu-id="bcfda-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress):`X-Forwarded-For` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="bcfda-131">追加の設定は、ミドルウェアが `RemoteIpAddress` を設定する方法に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="bcfda-132">詳しくは、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="bcfda-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme):`X-Forwarded-Proto` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="bcfda-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host):`X-Forwarded-Host` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="bcfda-135">Forwarded Headers Middleware の[既定の設定](#forwarded-headers-middleware-options)は構成できます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="bcfda-136">既定の設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="bcfda-136">The default settings are:</span></span>

* <span data-ttu-id="bcfda-137">アプリと要求のソースの間には、" *1 つのプロキシ* " だけが存在します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="bcfda-138">既知のプロキシと既知のネットワークに対しては、ループバック アドレスのみが構成されています。</span><span class="sxs-lookup"><span data-stu-id="bcfda-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="bcfda-139">転送されるヘッダーには `X-Forwarded-For` と `X-Forwarded-Proto` の名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="bcfda-140">すべてのネットワーク アプライアンスが追加構成なしで `X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを追加するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="bcfda-141">プロキシされた要求がアプリに届いたときにこれらのヘッダーが含まれていない場合は、アプライアンスの製造元のガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="bcfda-142">アプライアンスが `X-Forwarded-For` と `X-Forwarded-Proto` 以外の名前を使用している場合は、アプライアンスで使用されるヘッダー名と一致するように <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> と <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> のオプションを設定してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="bcfda-143">詳細については、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」と「[別のヘッダー名を使用するプロキシの構成](#configuration-for-a-proxy-that-uses-different-header-names)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="bcfda-144">IIS/IIS Express と ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="bcfda-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="bcfda-145">アプリが IIS と ASP.NET Core モジュールの背後で[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホストされている場合、[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) によって Forwarded Headers Middleware が既定で有効にされます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="bcfda-146">転送されるヘッダーの信頼に関する問題のため (たとえば、[IP スプーフィング](https://www.iplocation.net/ip-spoofing))、Forwarded Headers Middleware はアクティブ化されると最初に、ASP.NET Core モジュールに固有の制限された構成を使って、ミドルウェア パイプライン内で実行します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="bcfda-147">ミドルウェアは、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送するように構成され、単一の localhost プロキシに制限されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="bcfda-148">追加の構成が必要な場合は、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="bcfda-149">プロキシ サーバーとロード バランサーの他のシナリオ</span><span class="sxs-lookup"><span data-stu-id="bcfda-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="bcfda-150">[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホストされている場合に [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) が使われていない場合は、Forwarded Headers Middleware は既定で有効になりません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="bcfda-151"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を含む転送されたヘッダーをアプリで処理するためには、Forwarded Headers Middleware を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="bcfda-152">ミドルウェアを有効にした後、ミドルウェアに対して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が指定されていない場合の既定の [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) は [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="bcfda-153"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> でミドルウェアを構成して、`Startup.ConfigureServices` で `X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="bcfda-154">Forwarded Headers Middleware の順序</span><span class="sxs-lookup"><span data-stu-id="bcfda-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="bcfda-155">Forwarded Headers Middleware は、他のミドルウェアの前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="bcfda-156">この順序により、転送されるヘッダー情報に依存するミドルウェアが処理にヘッダー値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="bcfda-157">Forwarded Headers Middleware は、診断とエラー処理の後に実行できますが、`UseHsts` を呼び出す前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="bcfda-158">または、診断の前に `UseForwardedHeaders` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="bcfda-159"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が `Startup.ConfigureServices` において指定されていない場合、または <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を使って拡張メソッドに直接渡されない場合、転送される既定のヘッダーは [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="bcfda-160">転送するヘッダーで <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> プロパティが構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="bcfda-161">Nginx の構成</span><span class="sxs-lookup"><span data-stu-id="bcfda-161">Nginx configuration</span></span>

<span data-ttu-id="bcfda-162">`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送する場合は、<xref:host-and-deploy/linux-nginx#configure-nginx> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="bcfda-163">詳細については、「[NGINX:Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)」 (NGINX: 転送されるヘッダーの使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="bcfda-164">Apache の構成</span><span class="sxs-lookup"><span data-stu-id="bcfda-164">Apache configuration</span></span>

<span data-ttu-id="bcfda-165">`X-Forwarded-For` は自動的に追加されます (「[Apache Module mod_proxy:Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)」 (Apache モジュール mod_proxy: リバース プロキシがヘッダーを要求する) を参照)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="bcfda-166">`X-Forwarded-Proto` ヘッダーを転送する方法については、<xref:host-and-deploy/linux-apache#configure-apache> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="bcfda-167">Forwarded Headers Middleware のオプション</span><span class="sxs-lookup"><span data-stu-id="bcfda-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="bcfda-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> は、Forwarded Headers Middleware の動作を制御します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="bcfda-169">既定の値の変更例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-169">The following example changes the default values:</span></span>

* <span data-ttu-id="bcfda-170">転送されるヘッダーのエントリ数を `2` に制限します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="bcfda-171">`127.0.10.1` の既知のプロキシ アドレスを追加します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="bcfda-172">転送されるヘッダーの名前を既定の `X-Forwarded-For` から `X-Forwarded-For-My-Custom-Header-Name` に変更します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="bcfda-173">オプション</span><span class="sxs-lookup"><span data-stu-id="bcfda-173">Option</span></span> | <span data-ttu-id="bcfda-174">説明</span><span class="sxs-lookup"><span data-stu-id="bcfda-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="bcfda-175">`X-Forwarded-Host` ヘッダーによるホストを指定した値に制限します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="bcfda-176">値は、大文字と小文字を無視して序数で比較されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="bcfda-177">ポート番号は除外されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="bcfda-178">リストが空の場合、すべてのホストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="bcfda-179">最上位のワイルドカード `*` は、すべての空でないホストを許可します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="bcfda-180">サブドメインのワイルドカードは許可されますが、ルート ドメインとは一致しません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="bcfda-181">たとえば、`*.contoso.com` はサブドメイン `foo.contoso.com` と一致しますが、ルート ドメイン `contoso.com` とは一致しません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="bcfda-182">Unicode のホスト名は許可されますが、一致のために [Punycode](https://tools.ietf.org/html/rfc3492) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="bcfda-183">[IPv6 アドレス](https://tools.ietf.org/html/rfc4291)は境界の角かっこを含む必要があり、[従来の形式](https://tools.ietf.org/html/rfc4291#section-2.2) (例: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`) になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="bcfda-184">IPv6 アドレスは、異なる形式間で論理的な等価性を調べるために特別な大文字小文字にはされず、正規化は行われません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="bcfda-185">許可されるホストの制限に失敗すると、サービスによって生成されたリンクを攻撃者が偽装する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="bcfda-186">既定値は空の `IList<string>` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="bcfda-187">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="bcfda-188">このオプションは、プロキシ/フォワーダーが `X-Forwarded-For` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="bcfda-189">既定値は、`X-Forwarded-For` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="bcfda-190">処理する必要があるフォワーダーを識別します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="bcfda-191">適用されるフィールドの一覧については、「[ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)」(ForwardedHeaders 列挙型) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="bcfda-192">このプロパティに割り当てられる標準値は、`ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="bcfda-193">既定値は [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="bcfda-194">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="bcfda-195">このオプションは、プロキシ/フォワーダーが `X-Forwarded-Host` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="bcfda-196">既定値は、`X-Forwarded-Host` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="bcfda-197">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="bcfda-198">このオプションは、プロキシ/フォワーダーが `X-Forwarded-Proto` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="bcfda-199">既定値は、`X-Forwarded-Proto` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="bcfda-200">処理されるヘッダー内のエントリの数を制限します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="bcfda-201">制限を無効にするには `null` に設定しますが、これは `KnownProxies` または `KnownNetworks` が構成されている場合にのみ行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="bcfda-202">`null` 以外の値を設定することは、正しく構成されていないプロキシや、ネットワーク上のサイドチャネルから届く悪意のある要求から保護するための予防策となります (ただし保証はできません)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="bcfda-203">Forwarded Headers Middleware では、ヘッダーが右から左の逆の順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="bcfda-204">規定値 (`1`) を使う場合は、`ForwardLimit` の値を増やさない限りヘッダーの右端にある値のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="bcfda-205">既定値は、`1` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="bcfda-206">受け付けるヘッダーの転送元である既知のネットワークのアドレス範囲です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="bcfda-207">クラスレス ドメイン間ルーティング (CIDR) の表記を使って、IP の範囲を指定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="bcfda-208">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="bcfda-209">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="bcfda-210">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="bcfda-211">詳細については、「[IPv6 アドレスとして表される IPv4 アドレスの構成](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="bcfda-212">既定値は、`IPAddress.Loopback` の単一エントリを含む `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="bcfda-213">受け付けるヘッダーの転送元である既知のプロキシのアドレスです。</span><span class="sxs-lookup"><span data-stu-id="bcfda-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="bcfda-214">IP アドレスの正確な一致を指定するには、`KnownProxies` を使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="bcfda-215">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="bcfda-216">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="bcfda-217">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="bcfda-218">詳細については、「[IPv6 アドレスとして表される IPv4 アドレスの構成](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="bcfda-219">既定値は、`IPAddress.IPv6Loopback` の単一エントリを含む `IList`\<<xref:System.Net.IPAddress>> です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="bcfda-220">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="bcfda-221">既定値は、`X-Original-For` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="bcfda-222">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="bcfda-223">既定値は、`X-Original-Host` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="bcfda-224">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="bcfda-225">既定値は、`X-Original-Proto` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="bcfda-226">処理対象の [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) の間でヘッダー値の数が同期していることを要求します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="bcfda-227">ASP.NET Core 1.x での既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="bcfda-228">ASP.NET Core 2.0 以降での既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="bcfda-229">シナリオとユース ケース</span><span class="sxs-lookup"><span data-stu-id="bcfda-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="bcfda-230">転送されるヘッダーを追加することができず、すべての要求が安全な場合</span><span class="sxs-lookup"><span data-stu-id="bcfda-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="bcfda-231">アプリにプロキシされる要求に、転送されるヘッダーを追加できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="bcfda-232">すべてのパブリック外部要求が HTTPS を使うことをプロキシが強制している場合、すべての種類のミドルウェアを使う前に、`Startup.Configure` でスキームを手動で設定できます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="bcfda-233">このコードは、開発環境またはステージング環境の環境変数または他の構成設定によって、無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="bcfda-234">要求のパスを変更するパス ベースとプロキシを処理する</span><span class="sxs-lookup"><span data-stu-id="bcfda-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="bcfda-235">一部のプロキシでは、パスはそのまま渡されますが、ルーティングが正しく機能するためには削除する必要のあるアプリ ベース パスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="bcfda-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ミドルウェアは、パスを [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) と [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) へのアプリ ベース パスに分割します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="bcfda-237">`/foo` が `/foo/api/1` として渡されるプロキシ パスのアプリ ベース パスである場合、ミドルウェアは次のコマンドで `Request.PathBase` を `/foo`に、`Request.Path` を `/api/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="bcfda-238">ミドルウェアが逆方向にもう一度呼び出されると、元のパスとパス ベースが再度適用されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="bcfda-239">ミドルウェアの処理の順序について詳しくは、<xref:fundamentals/middleware/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="bcfda-240">プロキシがパスをトリミングする場合は (たとえば、`/foo/api/1` を `/api/1` に転送)、要求の [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) プロパティを設定することによって、リダイレクトとリンクを修正します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="bcfda-241">プロキシがパス データを追加している場合は、<xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> を使用して <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> プロパティに割り当てることで、パスの一部を破棄してリダイレクトとリンクを修正します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="bcfda-242">別のヘッダー名を使用するプロキシの構成</span><span class="sxs-lookup"><span data-stu-id="bcfda-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="bcfda-243">プロキシがプロキシ アドレス/ポートおよび開始スキーム情報の転送に名前が `X-Forwarded-For` と `X-Forwarded-Proto` のヘッダーを使用しない場合、プロキシで使用されるヘッダー名と一致するように、<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> と <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> のオプションを設定してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="bcfda-244">IPv6 アドレスとして表される IPv4 アドレスの構成</span><span class="sxs-lookup"><span data-stu-id="bcfda-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="bcfda-245">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` または `::ffff:a00:1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="bcfda-246">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="bcfda-247">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="bcfda-248">次の例では、転送されるヘッダーを提供するネットワーク アドレスを、IPv6 形式の `KnownNetworks` リストに追加します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="bcfda-249">IPv4 アドレス: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="bcfda-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="bcfda-250">変換された IPv6 アドレス: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="bcfda-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="bcfda-251">変換されたプレフィックス長:104</span><span class="sxs-lookup"><span data-stu-id="bcfda-251">Converted prefix length: 104</span></span>

<span data-ttu-id="bcfda-252">16 進数形式でアドレスを指定することもできます (`10.11.12.1` は IPv6 で `::ffff:0a0b:0c01` として表されます)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="bcfda-253">IPv4 アドレスを IPv6 に変換するときは、CIDR プレフィックス長 (例では `8`) に 96 を足して、追加の `::ffff:` IPv6 プレフィックスを構成します (8 + 96 = 104)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="bcfda-254">Linux および非 IIS のリバース プロキシのスキームを転送する</span><span class="sxs-lookup"><span data-stu-id="bcfda-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="bcfda-255"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> および <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> を呼び出すアプリが、Azure Linux App Service、Azure Linux 仮想マシン (VM)、または IIS 以外の他のリバース プロキシの背後に展開されている場合、サイトは無限ループに陥ります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="bcfda-256">TLS はリバース プロキシによって終了され、Kestrel では正しい要求スキームが認識されません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="bcfda-257">OAuth と OIDC もこの構成では正しくないリダイレクトを生成するため、失敗します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="bcfda-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> は IIS の背後で実行される場合、Forwarded Headers Middleware を追加して構成しますが、Linux 用の対応する自動設定 (Apache または Nginx 統合) はありません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="bcfda-259">非 IIS シナリオでプロキシからスキームを転送するには、Forwarded Headers Middleware を追加して構成します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="bcfda-260">`Startup.ConfigureServices` で、次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-260">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="bcfda-261">証明書の転送</span><span class="sxs-lookup"><span data-stu-id="bcfda-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="bcfda-262">Azure</span><span class="sxs-lookup"><span data-stu-id="bcfda-262">Azure</span></span>

<span data-ttu-id="bcfda-263">証明書の転送用に Azure App Service を構成するには、「[Azure App Service に対する TLS 相互認証の構成](/azure/app-service/app-service-web-configure-tls-mutual-auth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="bcfda-264">次のガイダンスは、ASP.NET Core アプリの構成に関するものです。</span><span class="sxs-lookup"><span data-stu-id="bcfda-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="bcfda-265">`Startup.Configure` で、`app.UseAuthentication();` の呼び出しの前に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="bcfda-266">Azure で使用されるヘッダー名を指定するように、証明書転送ミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="bcfda-267">`Startup.ConfigureServices` に次のコードを追加し、ミドルウェアによる証明書作成の基になるヘッダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="bcfda-268">他の Web プロキシ</span><span class="sxs-lookup"><span data-stu-id="bcfda-268">Other web proxies</span></span>

<span data-ttu-id="bcfda-269">使用されているプロキシが、IIS でも、Azure App Service のアプリケーション要求ルーティング処理 (ARR) でもない場合は、HTTP ヘッダーで受け取った証明書を転送するように、プロキシを構成します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="bcfda-270">`Startup.Configure` で、`app.UseAuthentication();` の呼び出しの前に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="bcfda-271">ヘッダー名を指定するように証明書の転送ミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="bcfda-272">`Startup.ConfigureServices` に次のコードを追加し、ミドルウェアによる証明書作成の基になるヘッダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="bcfda-273">プロキシで証明書が base64 エンコードではない場合は (Nginx の場合と同様)、`HeaderConverter` オプションを設定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="bcfda-274">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="bcfda-275">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bcfda-275">Troubleshoot</span></span>

<span data-ttu-id="bcfda-276">ヘッダーが意図したとおりに転送されない場合は、[ログ](xref:fundamentals/logging/index) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="bcfda-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="bcfda-277">ログで問題のトラブルシューティングに十分な情報が提供されない場合は、サーバーが受信した要求ヘッダーを列挙します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="bcfda-278">インライン ミドルウェアを使用し、アプリ応答に要求ヘッダーを書き込んだり、ヘッダーをログに記録したりします。</span><span class="sxs-lookup"><span data-stu-id="bcfda-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="bcfda-279">アプリの応答にヘッダーを書き込むには、`Startup.Configure` 内の <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> の呼び出しの直後に、次の端末のインライン ミドルウェアを配置します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="bcfda-280">応答本文ではなく、ログに書き込むことができます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="bcfda-281">ログに書き込むことで、デバッグしている間、サイトは正常に機能できます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="bcfda-282">応答本文ではなく、ログに書き込むには:</span><span class="sxs-lookup"><span data-stu-id="bcfda-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="bcfda-283">「[Startup でログを作成する](xref:fundamentals/logging/index#create-logs-in-startup)」に説明されているように、`ILogger<Startup>` を `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="bcfda-284">`Startup.Configure` 内で <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を呼び出した直後に次のインライン ミドルウェアを配置します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="bcfda-285">処理時、`X-Forwarded-{For|Proto|Host}` 値は `X-Original-{For|Proto|Host}` に移動されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="bcfda-286">指定されたヘッダーに複数の値がある場合、Forwarded Headers Middleware では右から左の逆の順序でヘッダーが処理されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="bcfda-287">既定の `ForwardLimit` は `1` です。そのため、`ForwardLimit` の値を増やさない限り、ヘッダーの右端にある値のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="bcfda-288">Forwarded Headers が処理される前に、要求の元のリモート IP アドレスが `KnownProxies` リストまたは `KnownNetworks` リストのエントリと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="bcfda-289">これにより、信頼されないプロキシからの転送を受け付けないことで、ヘッダーのスプーフィングを制限します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="bcfda-290">不明なプロキシが検出されると、ログにプロキシのアドレスが示されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="bcfda-291">前の例では、10.0.0.100 がプロキシ サーバーです。</span><span class="sxs-lookup"><span data-stu-id="bcfda-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="bcfda-292">サーバーが信頼されているプロキシであれば、`Startup.ConfigureServices` で `KnownProxies` にサーバーの IP アドレスを追加します (あるいは、信頼されているネットワークを `KnownNetworks` に追加します)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bcfda-293">詳細については、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="bcfda-294">信頼されているプロキシとネットワークにのみ、ヘッダーの転送を許可します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="bcfda-295">それ以外に許可すると、[IP なりすまし](https://www.iplocation.net/ip-spoofing)攻撃が可能になります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bcfda-296">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bcfda-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="bcfda-297">Microsoft セキュリティ アドバイザリ CVE-2018-0787:ASP.NET Core の特権の昇格脆弱性</span><span class="sxs-lookup"><span data-stu-id="bcfda-297">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bcfda-298">ASP.NET Core の推奨される構成では、アプリは IIS/ASP.NET Core モジュール、Nginx、または Apache を使ってホストされます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="bcfda-299">プロキシ サーバー、ロード バランサー、および他のネットワーク アプライアンスにより、要求に関する情報が、アプリに到達する前にわからなくなることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="bcfda-300">HTTPS 要求が HTTP によってプロキシされると、元のスキーム (HTTPS) は失われ、ヘッダーで転送される必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="bcfda-301">アプリは、インターネット上または社内ネットワーク上の本来の送信元ではなく、プロキシから要求を受信するため、送信元クライアントの IP アドレスもヘッダーで転送される必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="bcfda-302">この情報は、リダイレクト、認証、リンクの生成、ポリシーの評価、クライアントの位置情報など、要求の処理で重要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="bcfda-303">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="bcfda-303">Forwarded headers</span></span>

<span data-ttu-id="bcfda-304">慣例によりは、プロキシは HTTP ヘッダーで情報を転送します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="bcfda-305">Header</span><span class="sxs-lookup"><span data-stu-id="bcfda-305">Header</span></span> | <span data-ttu-id="bcfda-306">説明</span><span class="sxs-lookup"><span data-stu-id="bcfda-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="bcfda-307">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="bcfda-307">X-Forwarded-For</span></span> | <span data-ttu-id="bcfda-308">要求を開始したクライアントと、プロキシ チェーン内の後続のプロキシに関する情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="bcfda-309">このパラメーターは、IP アドレス (および、必要に応じてポート番号) を含むことがあります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="bcfda-310">プロキシ サーバーのチェーンにおいては、最初のパラメーターが、要求を最初に行ったクライアントを示します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="bcfda-311">その後に、後続のプロキシの識別子が指定されています。</span><span class="sxs-lookup"><span data-stu-id="bcfda-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="bcfda-312">チェーン内の最後のプロキシは、パラメーターのリストには含まれません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="bcfda-313">最後のプロキシの IP アドレスとオプションのポート番号は、トランスポート層のリモート IP アドレスとして入手できます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="bcfda-314">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="bcfda-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="bcfda-315">開始スキーム (HTTP/HTTPS) の値です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="bcfda-316">要求が複数のプロキシを通過している場合、値はスキームのリストである場合もあります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="bcfda-317">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="bcfda-317">X-Forwarded-Host</span></span> | <span data-ttu-id="bcfda-318">ホスト ヘッダー フィールドの元の値です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-318">The original value of the Host header field.</span></span> <span data-ttu-id="bcfda-319">通常、プロキシはホスト ヘッダーを変更しません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="bcfda-320">プロキシにおいてホスト ヘッダーが既知の適切な値であることが検証されない、または既知の適切な値に制限されないシステムに影響を与える、特権の昇格脆弱性については、[マイクロソフト セキュリティ アドバイザリ CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="bcfda-321">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) パッケージの Forwarded Headers Middleware は、これらのヘッダーを読み取り、<xref:Microsoft.AspNetCore.Http.HttpContext> の関連するフィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="bcfda-322">ミドルウェアの更新:</span><span class="sxs-lookup"><span data-stu-id="bcfda-322">The middleware updates:</span></span>

* <span data-ttu-id="bcfda-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress):`X-Forwarded-For` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="bcfda-324">追加の設定は、ミドルウェアが `RemoteIpAddress` を設定する方法に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="bcfda-325">詳しくは、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="bcfda-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme):`X-Forwarded-Proto` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="bcfda-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host):`X-Forwarded-Host` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="bcfda-328">Forwarded Headers Middleware の[既定の設定](#forwarded-headers-middleware-options)は構成できます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="bcfda-329">既定の設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="bcfda-329">The default settings are:</span></span>

* <span data-ttu-id="bcfda-330">アプリと要求のソースの間には、" *1 つのプロキシ* " だけが存在します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="bcfda-331">既知のプロキシと既知のネットワークに対しては、ループバック アドレスのみが構成されています。</span><span class="sxs-lookup"><span data-stu-id="bcfda-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="bcfda-332">転送されるヘッダーには `X-Forwarded-For` と `X-Forwarded-Proto` の名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="bcfda-333">すべてのネットワーク アプライアンスが追加構成なしで `X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを追加するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="bcfda-334">プロキシされた要求がアプリに届いたときにこれらのヘッダーが含まれていない場合は、アプライアンスの製造元のガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="bcfda-335">アプライアンスが `X-Forwarded-For` と `X-Forwarded-Proto` 以外の名前を使用している場合は、アプライアンスで使用されるヘッダー名と一致するように <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> と <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> のオプションを設定してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="bcfda-336">詳細については、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」と「[別のヘッダー名を使用するプロキシの構成](#configuration-for-a-proxy-that-uses-different-header-names)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="bcfda-337">IIS/IIS Express と ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="bcfda-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="bcfda-338">アプリが IIS と ASP.NET Core モジュールの背後で[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホストされている場合、[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) によって Forwarded Headers Middleware が既定で有効にされます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="bcfda-339">転送されるヘッダーの信頼に関する問題のため (たとえば、[IP スプーフィング](https://www.iplocation.net/ip-spoofing))、Forwarded Headers Middleware はアクティブ化されると最初に、ASP.NET Core モジュールに固有の制限された構成を使って、ミドルウェア パイプライン内で実行します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="bcfda-340">ミドルウェアは、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送するように構成され、単一の localhost プロキシに制限されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="bcfda-341">追加の構成が必要な場合は、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="bcfda-342">プロキシ サーバーとロード バランサーの他のシナリオ</span><span class="sxs-lookup"><span data-stu-id="bcfda-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="bcfda-343">[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホストされている場合に [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) が使われていない場合は、Forwarded Headers Middleware は既定で有効になりません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="bcfda-344"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を含む転送されたヘッダーをアプリで処理するためには、Forwarded Headers Middleware を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="bcfda-345">ミドルウェアを有効にした後、ミドルウェアに対して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が指定されていない場合の既定の [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) は [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="bcfda-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> でミドルウェアを構成して、`Startup.ConfigureServices` で `X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bcfda-347">他のミドルウェアを呼び出す前に、`Startup.Configure` 内で <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.Use:::no-loc(Identity):::();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="bcfda-348"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が `Startup.ConfigureServices` において指定されていない場合、または <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を使って拡張メソッドに直接渡されない場合、転送される既定のヘッダーは [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="bcfda-349">転送するヘッダーで <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> プロパティが構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="bcfda-350">Nginx の構成</span><span class="sxs-lookup"><span data-stu-id="bcfda-350">Nginx configuration</span></span>

<span data-ttu-id="bcfda-351">`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送する場合は、<xref:host-and-deploy/linux-nginx#configure-nginx> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="bcfda-352">詳細については、「[NGINX:Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)」 (NGINX: 転送されるヘッダーの使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="bcfda-353">Apache の構成</span><span class="sxs-lookup"><span data-stu-id="bcfda-353">Apache configuration</span></span>

<span data-ttu-id="bcfda-354">`X-Forwarded-For` は自動的に追加されます (「[Apache Module mod_proxy:Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)」 (Apache モジュール mod_proxy: リバース プロキシがヘッダーを要求する) を参照)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="bcfda-355">`X-Forwarded-Proto` ヘッダーを転送する方法については、<xref:host-and-deploy/linux-apache#configure-apache> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="bcfda-356">Forwarded Headers Middleware のオプション</span><span class="sxs-lookup"><span data-stu-id="bcfda-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="bcfda-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> は、Forwarded Headers Middleware の動作を制御します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="bcfda-358">既定の値の変更例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-358">The following example changes the default values:</span></span>

* <span data-ttu-id="bcfda-359">転送されるヘッダーのエントリ数を `2` に制限します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="bcfda-360">`127.0.10.1` の既知のプロキシ アドレスを追加します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="bcfda-361">転送されるヘッダーの名前を既定の `X-Forwarded-For` から `X-Forwarded-For-My-Custom-Header-Name` に変更します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="bcfda-362">オプション</span><span class="sxs-lookup"><span data-stu-id="bcfda-362">Option</span></span> | <span data-ttu-id="bcfda-363">説明</span><span class="sxs-lookup"><span data-stu-id="bcfda-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="bcfda-364">`X-Forwarded-Host` ヘッダーによるホストを指定した値に制限します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="bcfda-365">値は、大文字と小文字を無視して序数で比較されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="bcfda-366">ポート番号は除外されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="bcfda-367">リストが空の場合、すべてのホストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="bcfda-368">最上位のワイルドカード `*` は、すべての空でないホストを許可します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="bcfda-369">サブドメインのワイルドカードは許可されますが、ルート ドメインとは一致しません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="bcfda-370">たとえば、`*.contoso.com` はサブドメイン `foo.contoso.com` と一致しますが、ルート ドメイン `contoso.com` とは一致しません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="bcfda-371">Unicode のホスト名は許可されますが、一致のために [Punycode](https://tools.ietf.org/html/rfc3492) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="bcfda-372">[IPv6 アドレス](https://tools.ietf.org/html/rfc4291)は境界の角かっこを含む必要があり、[従来の形式](https://tools.ietf.org/html/rfc4291#section-2.2) (例: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`) になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="bcfda-373">IPv6 アドレスは、異なる形式間で論理的な等価性を調べるために特別な大文字小文字にはされず、正規化は行われません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="bcfda-374">許可されるホストの制限に失敗すると、サービスによって生成されたリンクを攻撃者が偽装する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="bcfda-375">既定値は空の `IList<string>` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="bcfda-376">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="bcfda-377">このオプションは、プロキシ/フォワーダーが `X-Forwarded-For` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="bcfda-378">既定値は、`X-Forwarded-For` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="bcfda-379">処理する必要があるフォワーダーを識別します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="bcfda-380">適用されるフィールドの一覧については、「[ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)」(ForwardedHeaders 列挙型) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="bcfda-381">このプロパティに割り当てられる標準値は、`ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="bcfda-382">既定値は [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="bcfda-383">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="bcfda-384">このオプションは、プロキシ/フォワーダーが `X-Forwarded-Host` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="bcfda-385">既定値は、`X-Forwarded-Host` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="bcfda-386">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="bcfda-387">このオプションは、プロキシ/フォワーダーが `X-Forwarded-Proto` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="bcfda-388">既定値は、`X-Forwarded-Proto` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="bcfda-389">処理されるヘッダー内のエントリの数を制限します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="bcfda-390">制限を無効にするには `null` に設定しますが、これは `KnownProxies` または `KnownNetworks` が構成されている場合にのみ行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="bcfda-391">`null` 以外の値を設定することは、正しく構成されていないプロキシや、ネットワーク上のサイドチャネルから届く悪意のある要求から保護するための予防策となります (ただし保証はできません)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="bcfda-392">Forwarded Headers Middleware では、ヘッダーが右から左の逆の順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="bcfda-393">規定値 (`1`) を使う場合は、`ForwardLimit` の値を増やさない限りヘッダーの右端にある値のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="bcfda-394">既定値は、`1` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="bcfda-395">受け付けるヘッダーの転送元である既知のネットワークのアドレス範囲です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="bcfda-396">クラスレス ドメイン間ルーティング (CIDR) の表記を使って、IP の範囲を指定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="bcfda-397">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="bcfda-398">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="bcfda-399">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="bcfda-400">詳細については、「[IPv6 アドレスとして表される IPv4 アドレスの構成](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="bcfda-401">既定値は、`IPAddress.Loopback` の単一エントリを含む `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="bcfda-402">受け付けるヘッダーの転送元である既知のプロキシのアドレスです。</span><span class="sxs-lookup"><span data-stu-id="bcfda-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="bcfda-403">IP アドレスの正確な一致を指定するには、`KnownProxies` を使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="bcfda-404">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="bcfda-405">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="bcfda-406">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="bcfda-407">詳細については、「[IPv6 アドレスとして表される IPv4 アドレスの構成](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="bcfda-408">既定値は、`IPAddress.IPv6Loopback` の単一エントリを含む `IList`\<<xref:System.Net.IPAddress>> です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="bcfda-409">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="bcfda-410">既定値は、`X-Original-For` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="bcfda-411">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="bcfda-412">既定値は、`X-Original-Host` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="bcfda-413">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="bcfda-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="bcfda-414">既定値は、`X-Original-Proto` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="bcfda-415">処理対象の [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) の間でヘッダー値の数が同期していることを要求します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="bcfda-416">ASP.NET Core 1.x での既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="bcfda-417">ASP.NET Core 2.0 以降での既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="bcfda-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="bcfda-418">シナリオとユース ケース</span><span class="sxs-lookup"><span data-stu-id="bcfda-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="bcfda-419">転送されるヘッダーを追加することができず、すべての要求が安全な場合</span><span class="sxs-lookup"><span data-stu-id="bcfda-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="bcfda-420">アプリにプロキシされる要求に、転送されるヘッダーを追加できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="bcfda-421">すべてのパブリック外部要求が HTTPS を使うことをプロキシが強制している場合、すべての種類のミドルウェアを使う前に、`Startup.Configure` でスキームを手動で設定できます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="bcfda-422">このコードは、開発環境またはステージング環境の環境変数または他の構成設定によって、無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="bcfda-423">要求のパスを変更するパス ベースとプロキシを処理する</span><span class="sxs-lookup"><span data-stu-id="bcfda-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="bcfda-424">一部のプロキシでは、パスはそのまま渡されますが、ルーティングが正しく機能するためには削除する必要のあるアプリ ベース パスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="bcfda-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ミドルウェアは、パスを [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) と [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) へのアプリ ベース パスに分割します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="bcfda-426">`/foo` が `/foo/api/1` として渡されるプロキシ パスのアプリ ベース パスである場合、ミドルウェアは次のコマンドで `Request.PathBase` を `/foo`に、`Request.Path` を `/api/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="bcfda-427">ミドルウェアが逆方向にもう一度呼び出されると、元のパスとパス ベースが再度適用されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="bcfda-428">ミドルウェアの処理の順序について詳しくは、<xref:fundamentals/middleware/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="bcfda-429">プロキシがパスをトリミングする場合は (たとえば、`/foo/api/1` を `/api/1` に転送)、要求の [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) プロパティを設定することによって、リダイレクトとリンクを修正します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="bcfda-430">プロキシがパス データを追加している場合は、<xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> を使用して <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> プロパティに割り当てることで、パスの一部を破棄してリダイレクトとリンクを修正します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="bcfda-431">別のヘッダー名を使用するプロキシの構成</span><span class="sxs-lookup"><span data-stu-id="bcfda-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="bcfda-432">プロキシがプロキシ アドレス/ポートおよび開始スキーム情報の転送に名前が `X-Forwarded-For` と `X-Forwarded-Proto` のヘッダーを使用しない場合、プロキシで使用されるヘッダー名と一致するように、<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> と <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> のオプションを設定してください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="bcfda-433">IPv6 アドレスとして表される IPv4 アドレスの構成</span><span class="sxs-lookup"><span data-stu-id="bcfda-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="bcfda-434">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` または `::ffff:a00:1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="bcfda-435">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="bcfda-436">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="bcfda-437">次の例では、転送されるヘッダーを提供するネットワーク アドレスを、IPv6 形式の `KnownNetworks` リストに追加します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="bcfda-438">IPv4 アドレス: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="bcfda-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="bcfda-439">変換された IPv6 アドレス: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="bcfda-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="bcfda-440">変換されたプレフィックス長:104</span><span class="sxs-lookup"><span data-stu-id="bcfda-440">Converted prefix length: 104</span></span>

<span data-ttu-id="bcfda-441">16 進数形式でアドレスを指定することもできます (`10.11.12.1` は IPv6 で `::ffff:0a0b:0c01` として表されます)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="bcfda-442">IPv4 アドレスを IPv6 に変換するときは、CIDR プレフィックス長 (例では `8`) に 96 を足して、追加の `::ffff:` IPv6 プレフィックスを構成します (8 + 96 = 104)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="bcfda-443">Linux および非 IIS のリバース プロキシのスキームを転送する</span><span class="sxs-lookup"><span data-stu-id="bcfda-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="bcfda-444"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> および <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> を呼び出すアプリが、Azure Linux App Service、Azure Linux 仮想マシン (VM)、または IIS 以外の他のリバース プロキシの背後に展開されている場合、サイトは無限ループに陥ります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="bcfda-445">TLS はリバース プロキシによって終了され、Kestrel では正しい要求スキームが認識されません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="bcfda-446">OAuth と OIDC もこの構成では正しくないリダイレクトを生成するため、失敗します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="bcfda-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> は IIS の背後で実行される場合、Forwarded Headers Middleware を追加して構成しますが、Linux 用の対応する自動設定 (Apache または Nginx 統合) はありません。</span><span class="sxs-lookup"><span data-stu-id="bcfda-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="bcfda-448">非 IIS シナリオでプロキシからスキームを転送するには、Forwarded Headers Middleware を追加して構成します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="bcfda-449">`Startup.ConfigureServices` で、次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-449">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="bcfda-450">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bcfda-450">Troubleshoot</span></span>

<span data-ttu-id="bcfda-451">ヘッダーが意図したとおりに転送されない場合は、[ログ](xref:fundamentals/logging/index) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="bcfda-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="bcfda-452">ログで問題のトラブルシューティングに十分な情報が提供されない場合は、サーバーが受信した要求ヘッダーを列挙します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="bcfda-453">インライン ミドルウェアを使用し、アプリ応答に要求ヘッダーを書き込んだり、ヘッダーをログに記録したりします。</span><span class="sxs-lookup"><span data-stu-id="bcfda-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="bcfda-454">アプリの応答にヘッダーを書き込むには、`Startup.Configure` 内の <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> の呼び出しの直後に、次の端末のインライン ミドルウェアを配置します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="bcfda-455">応答本文ではなく、ログに書き込むことができます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="bcfda-456">ログに書き込むことで、デバッグしている間、サイトは正常に機能できます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="bcfda-457">応答本文ではなく、ログに書き込むには:</span><span class="sxs-lookup"><span data-stu-id="bcfda-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="bcfda-458">「[Startup でログを作成する](xref:fundamentals/logging/index#create-logs-in-startup)」に説明されているように、`ILogger<Startup>` を `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="bcfda-459">`Startup.Configure` 内で <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を呼び出した直後に次のインライン ミドルウェアを配置します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="bcfda-460">処理時、`X-Forwarded-{For|Proto|Host}` 値は `X-Original-{For|Proto|Host}` に移動されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="bcfda-461">指定されたヘッダーに複数の値がある場合、Forwarded Headers Middleware では右から左の逆の順序でヘッダーが処理されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="bcfda-462">既定の `ForwardLimit` は `1` です。そのため、`ForwardLimit` の値を増やさない限り、ヘッダーの右端にある値のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="bcfda-463">Forwarded Headers が処理される前に、要求の元のリモート IP アドレスが `KnownProxies` リストまたは `KnownNetworks` リストのエントリと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="bcfda-464">これにより、信頼されないプロキシからの転送を受け付けないことで、ヘッダーのスプーフィングを制限します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="bcfda-465">不明なプロキシが検出されると、ログにプロキシのアドレスが示されます。</span><span class="sxs-lookup"><span data-stu-id="bcfda-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="bcfda-466">前の例では、10.0.0.100 がプロキシ サーバーです。</span><span class="sxs-lookup"><span data-stu-id="bcfda-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="bcfda-467">サーバーが信頼されているプロキシであれば、`Startup.ConfigureServices` で `KnownProxies` にサーバーの IP アドレスを追加します (あるいは、信頼されているネットワークを `KnownNetworks` に追加します)。</span><span class="sxs-lookup"><span data-stu-id="bcfda-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bcfda-468">詳細については、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bcfda-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="bcfda-469">信頼されているプロキシとネットワークにのみ、ヘッダーの転送を許可します。</span><span class="sxs-lookup"><span data-stu-id="bcfda-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="bcfda-470">それ以外に許可すると、[IP なりすまし](https://www.iplocation.net/ip-spoofing)攻撃が可能になります。</span><span class="sxs-lookup"><span data-stu-id="bcfda-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bcfda-471">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bcfda-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="bcfda-472">Microsoft セキュリティ アドバイザリ CVE-2018-0787:ASP.NET Core の特権の昇格脆弱性</span><span class="sxs-lookup"><span data-stu-id="bcfda-472">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
