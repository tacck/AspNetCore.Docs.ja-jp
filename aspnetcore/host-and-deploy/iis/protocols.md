---
title: IIS 上で HTTP/2 と共に ASP.NET Core を使用する
author: rick-anderson
description: IIS で HTTP/2 機能を使用する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057415"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="d4956-103">IIS 上で HTTP/2 と共に ASP.NET Core を使用する</span><span class="sxs-lookup"><span data-stu-id="d4956-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="d4956-104">作成者: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="d4956-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="d4956-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d4956-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="d4956-106">Windows Server 2016 以降、Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="d4956-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="d4956-107">IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="d4956-107">IIS 10 or later</span></span>
* <span data-ttu-id="d4956-108">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d4956-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="d4956-109">[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホスティングしている場合: 一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d4956-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="d4956-110">インプロセスの展開の場合、HTTP/2 接続が確立されると、[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d4956-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="d4956-111">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d4956-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="d4956-112">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4956-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d4956-113">HTTPS/TLS 接続においては、既定で HTTP/2 が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="d4956-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="d4956-114">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d4956-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="d4956-115">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4956-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="d4956-116">gRPC をサポートする高度な HTTP/2 機能</span><span class="sxs-lookup"><span data-stu-id="d4956-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="d4956-117">IIS 内の追加の HTTP/2 機能により、gRPC がサポートされています。これには、応答トレーラーやリセット フレームの送信に関するサポートが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4956-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="d4956-118">IIS 上で gRPC を実行するための要件:</span><span class="sxs-lookup"><span data-stu-id="d4956-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="d4956-119">インプロセス ホスティング。</span><span class="sxs-lookup"><span data-stu-id="d4956-119">In-process hosting.</span></span>
* <span data-ttu-id="d4956-120">Windows 10、OS Build 20300.1000 以降。</span><span class="sxs-lookup"><span data-stu-id="d4956-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="d4956-121">Windows Insider ビルドの使用が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d4956-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="d4956-122">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d4956-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="d4956-123">予告編</span><span class="sxs-lookup"><span data-stu-id="d4956-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="d4956-124">Reset</span><span class="sxs-lookup"><span data-stu-id="d4956-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
