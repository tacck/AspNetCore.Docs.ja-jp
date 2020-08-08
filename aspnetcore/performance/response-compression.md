---
title: ASP.NET Core での応答圧縮
author: rick-anderson
description: 応答圧縮と ASP.NET Core アプリで応答圧縮ミドルウェアを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/response-compression
ms.openlocfilehash: 1dd931d0ee654b888814df8a0d0675d32b5c3a20
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020965"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="8453e-103">ASP.NET Core での応答圧縮</span><span class="sxs-lookup"><span data-stu-id="8453e-103">Response compression in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8453e-104">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="8453e-104">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="8453e-105">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="8453e-105">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="8453e-106">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="8453e-106">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="8453e-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8453e-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="8453e-108">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="8453e-108">When to use Response Compression Middleware</span></span>

<span data-ttu-id="8453e-109">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="8453e-109">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="8453e-110">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-110">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="8453e-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) Server と[kestrel](xref:fundamentals/servers/kestrel) server では、現在、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="8453e-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="8453e-112">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="8453e-112">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="8453e-113">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="8453e-113">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="8453e-114">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="8453e-114">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="8453e-115">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="8453e-115">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="8453e-116">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="8453e-116">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="8453e-117">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="8453e-117">Hosting directly on:</span></span>
  * <span data-ttu-id="8453e-118">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="8453e-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="8453e-119">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="8453e-119">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="8453e-120">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="8453e-120">Response compression</span></span>

<span data-ttu-id="8453e-121">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="8453e-121">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="8453e-122">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8453e-122">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="8453e-123">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="8453e-123">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="8453e-124">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="8453e-124">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="8453e-125">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="8453e-125">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="8453e-126">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-126">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="8453e-127">クライアントは、圧縮されたコンテンツを処理できる場合、 `Accept-Encoding` 要求と共にヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-127">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="8453e-128">サーバーは、圧縮されたコンテンツを送信するときに、 `Content-Encoding` 圧縮された応答のエンコード方法に関する情報をヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-128">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="8453e-129">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-129">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="8453e-130">`Accept-Encoding`ヘッダー値</span><span class="sxs-lookup"><span data-stu-id="8453e-130">`Accept-Encoding` header values</span></span> | <span data-ttu-id="8453e-131">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="8453e-131">Middleware Supported</span></span> | <span data-ttu-id="8453e-132">説明</span><span class="sxs-lookup"><span data-stu-id="8453e-132">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="8453e-133">あり (既定)</span><span class="sxs-lookup"><span data-stu-id="8453e-133">Yes (default)</span></span>        | [<span data-ttu-id="8453e-134">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="8453e-134">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="8453e-135">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-135">No</span></span>                   | [<span data-ttu-id="8453e-136">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="8453e-136">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="8453e-137">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-137">No</span></span>                   | [<span data-ttu-id="8453e-138">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="8453e-138">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="8453e-139">はい</span><span class="sxs-lookup"><span data-stu-id="8453e-139">Yes</span></span>                  | [<span data-ttu-id="8453e-140">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="8453e-140">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="8453e-141">はい</span><span class="sxs-lookup"><span data-stu-id="8453e-141">Yes</span></span>                  | <span data-ttu-id="8453e-142">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8453e-142">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="8453e-143">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-143">No</span></span>                   | [<span data-ttu-id="8453e-144">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="8453e-144">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="8453e-145">はい</span><span class="sxs-lookup"><span data-stu-id="8453e-145">Yes</span></span>                  | <span data-ttu-id="8453e-146">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="8453e-146">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="8453e-147">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-147">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="8453e-148">ミドルウェアを使用すると、カスタムヘッダー値の圧縮プロバイダーを追加でき `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-148">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="8453e-149">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-149">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="8453e-150">ミドルウェアは、 `q` 圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue,) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="8453e-150">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="8453e-151">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-151">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="8453e-152">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="8453e-152">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="8453e-153">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-153">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="8453e-154">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-154">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="8453e-155">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="8453e-155">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="8453e-156">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="8453e-156">Header</span></span>             | <span data-ttu-id="8453e-157">Role</span><span class="sxs-lookup"><span data-stu-id="8453e-157">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="8453e-158">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-158">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="8453e-159">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-159">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="8453e-160">圧縮が発生すると、 `Content-Length` 応答が圧縮されるときに本文の内容が変更されるため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-160">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="8453e-161">圧縮が行われると、 `Content-MD5` 本文の内容が変更され、ハッシュが有効でなくなったため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-161">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="8453e-162">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="8453e-162">Specifies the MIME type of the content.</span></span> <span data-ttu-id="8453e-163">すべての応答で、を指定する必要があり `Content-Type` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-163">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="8453e-164">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8453e-164">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="8453e-165">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="8453e-165">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="8453e-166">の値がであるサーバーからクライアントとプロキシに送信された場合 `Accept-Encoding` 、ヘッダーは、 `Vary` 要求のヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示し `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-166">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="8453e-167">ヘッダーを使用してコンテンツを返すと、 `Vary: Accept-Encoding` 圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="8453e-167">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="8453e-168">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="8453e-168">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="8453e-169">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-169">The sample illustrates:</span></span>

* <span data-ttu-id="8453e-170">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="8453e-170">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="8453e-171">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8453e-171">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="8453e-172">Package</span><span class="sxs-lookup"><span data-stu-id="8453e-172">Package</span></span>

<span data-ttu-id="8453e-173">応答圧縮ミドルウェアは、AspNetCore アプリ ASP.NET Core に暗黙的に含まれる[ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-173">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="8453e-174">構成</span><span class="sxs-lookup"><span data-stu-id="8453e-174">Configuration</span></span>

<span data-ttu-id="8453e-175">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="8453e-175">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="8453e-176">メモ:</span><span class="sxs-lookup"><span data-stu-id="8453e-176">Notes:</span></span>

* <span data-ttu-id="8453e-177">`app.UseResponseCompression`応答を圧縮するミドルウェアの前にを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-177">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="8453e-178">詳細については、「<xref:fundamentals/middleware/index#middleware-order>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-178">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="8453e-179">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用して要求ヘッダーを設定 `Accept-Encoding` し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="8453e-179">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="8453e-180">ヘッダーを使用せずにサンプルアプリに要求を送信 `Accept-Encoding` し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8453e-180">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="8453e-181">`Content-Encoding` `Vary` 応答にヘッダーとヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="8453e-181">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="8453e-184">ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信 `Accept-Encoding: br` し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8453e-184">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="8453e-185">`Content-Encoding` `Vary` 応答には、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="8453e-185">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="8453e-189">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-189">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="8453e-190">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-190">Brotli Compression Provider</span></span>

<span data-ttu-id="8453e-191"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="8453e-191">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="8453e-192">圧縮プロバイダーがに明示的に追加されていない場合は、次のように <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> なります。</span><span class="sxs-lookup"><span data-stu-id="8453e-192">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="8453e-193">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-193">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="8453e-194">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="8453e-194">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="8453e-195">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="8453e-195">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="8453e-196">圧縮プロバイダーが明示的に追加されている場合は、Brotli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-196">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="8453e-197">圧縮レベルをに設定 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> します。</span><span class="sxs-lookup"><span data-stu-id="8453e-197">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="8453e-198">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-198">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="8453e-199">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="8453e-199">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="8453e-200">Compression Level</span><span class="sxs-lookup"><span data-stu-id="8453e-200">Compression Level</span></span> | <span data-ttu-id="8453e-201">説明</span><span class="sxs-lookup"><span data-stu-id="8453e-201">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="8453e-202">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-202">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-203">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-203">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="8453e-204">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-204">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-205">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="8453e-205">No compression should be performed.</span></span> |
| [<span data-ttu-id="8453e-206">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-206">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-207">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-207">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a><span data-ttu-id="8453e-208">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-208">Gzip Compression Provider</span></span>

<span data-ttu-id="8453e-209">を使用して、 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="8453e-209">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="8453e-210">圧縮プロバイダーがに明示的に追加されていない場合は、次のように <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> なります。</span><span class="sxs-lookup"><span data-stu-id="8453e-210">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="8453e-211">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-211">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="8453e-212">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="8453e-212">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="8453e-213">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="8453e-213">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="8453e-214">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-214">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="8453e-215">圧縮レベルをに設定 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> します。</span><span class="sxs-lookup"><span data-stu-id="8453e-215">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="8453e-216">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-216">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="8453e-217">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="8453e-217">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="8453e-218">Compression Level</span><span class="sxs-lookup"><span data-stu-id="8453e-218">Compression Level</span></span> | <span data-ttu-id="8453e-219">説明</span><span class="sxs-lookup"><span data-stu-id="8453e-219">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="8453e-220">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-220">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-221">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-221">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="8453e-222">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-222">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-223">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="8453e-223">No compression should be performed.</span></span> |
| [<span data-ttu-id="8453e-224">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-224">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-225">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-225">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="8453e-226">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-226">Custom providers</span></span>

<span data-ttu-id="8453e-227">を使用して、カスタム圧縮実装を作成 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> します。</span><span class="sxs-lookup"><span data-stu-id="8453e-227">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="8453e-228">は、 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> このによって生成されるコンテンツエンコーディングを表し `ICompressionProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-228">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="8453e-229">ミドルウェアは、この情報を使用して、要求のヘッダーに指定されている一覧に基づいてプロバイダーを選択し `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-229">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="8453e-230">サンプルアプリを使用して、クライアントはヘッダーを使用して要求を送信し `Accept-Encoding: mycustomcompression` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-230">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="8453e-231">ミドルウェアはカスタム圧縮実装を使用して、応答をヘッダーと共に返し `Content-Encoding: mycustomcompression` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-231">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="8453e-232">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-232">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="8453e-233">ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認し `Accept-Encoding: mycustomcompression` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-233">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="8453e-234">`Vary` `Content-Encoding` 応答には、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="8453e-234">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="8453e-235">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="8453e-235">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="8453e-236">サンプルのクラスには圧縮実装がありません `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="8453e-236">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="8453e-237">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="8453e-237">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="8453e-240">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="8453e-240">MIME types</span></span>

<span data-ttu-id="8453e-241">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="8453e-241">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="8453e-242">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="8453e-242">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="8453e-243">などのワイルドカードの MIME 型はサポートされていないことに注意して `text/*` ください。</span><span class="sxs-lookup"><span data-stu-id="8453e-243">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="8453e-244">このサンプルアプリでは、の MIME の種類を追加 `image/svg+xml` し、ASP.NET Core バナーイメージ (*横断幕*) を圧縮して提供します。</span><span class="sxs-lookup"><span data-stu-id="8453e-244">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="8453e-245">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="8453e-245">Compression with secure protocol</span></span>

<span data-ttu-id="8453e-246">セキュリティで保護された接続での圧縮された応答は、 `EnableForHttps` 既定では無効になっているオプションを使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="8453e-246">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="8453e-247">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-247">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="8453e-248">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="8453e-248">Adding the Vary header</span></span>

<span data-ttu-id="8453e-249">ヘッダーに基づいて応答を圧縮する場合 `Accept-Encoding` 、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-249">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="8453e-250">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、 `Vary` ヘッダーに値を追加し `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-250">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="8453e-251">ASP.NET Core 2.0 以降では、 `Vary` 応答が圧縮されるときに、ミドルウェアによってヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-251">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="8453e-252">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="8453e-252">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="8453e-253">要求が Nginx によってプロキシされている場合、 `Accept-Encoding` ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-253">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="8453e-254">ヘッダーを削除すると、 `Accept-Encoding` ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="8453e-254">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="8453e-255">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-255">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="8453e-256">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-256">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="8453e-257">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="8453e-257">Working with IIS dynamic compression</span></span>

<span data-ttu-id="8453e-258">アプリケーションに対して無効にする、アクティブな IIS 動的圧縮モジュールがサーバーレベルで構成されている場合は、 *web.config*ファイルを追加してモジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="8453e-258">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="8453e-259">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-259">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8453e-260">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="8453e-260">Troubleshooting</span></span>

<span data-ttu-id="8453e-261">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用します。これにより、 `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="8453e-261">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="8453e-262">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="8453e-262">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="8453e-263">`Accept-Encoding`ヘッダーには `br` 、設定した `gzip` `*` カスタム圧縮プロバイダーに一致する値、、、またはカスタムエンコーディングが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8453e-263">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="8453e-264">値は、 `identity` または品質値 (qvalue, `q` ) の 0 (ゼロ) に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="8453e-264">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="8453e-265">MIME の種類 ( `Content-Type` ) を設定し、で構成されている mime の種類と一致させる必要があり <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-265">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="8453e-266">要求にヘッダーを含めることはできません `Content-Range` 。</span><span class="sxs-lookup"><span data-stu-id="8453e-266">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="8453e-267">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-267">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="8453e-268">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="8453e-268">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8453e-269">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8453e-269">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="8453e-270">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="8453e-270">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="8453e-271">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="8453e-271">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="8453e-272">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="8453e-272">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="8453e-273">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="8453e-273">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="8453e-274">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="8453e-274">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="8453e-275">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="8453e-275">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="8453e-276">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="8453e-276">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="8453e-277">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8453e-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="8453e-278">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="8453e-278">When to use Response Compression Middleware</span></span>

<span data-ttu-id="8453e-279">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="8453e-279">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="8453e-280">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-280">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="8453e-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) Server と[kestrel](xref:fundamentals/servers/kestrel) server では、現在、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="8453e-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="8453e-282">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="8453e-282">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="8453e-283">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="8453e-283">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="8453e-284">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="8453e-284">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="8453e-285">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="8453e-285">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="8453e-286">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="8453e-286">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="8453e-287">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="8453e-287">Hosting directly on:</span></span>
  * <span data-ttu-id="8453e-288">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="8453e-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="8453e-289">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="8453e-289">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="8453e-290">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="8453e-290">Response compression</span></span>

<span data-ttu-id="8453e-291">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="8453e-291">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="8453e-292">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8453e-292">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="8453e-293">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="8453e-293">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="8453e-294">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="8453e-294">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="8453e-295">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="8453e-295">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="8453e-296">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-296">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="8453e-297">クライアントは、圧縮されたコンテンツを処理できる場合、 `Accept-Encoding` 要求と共にヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-297">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="8453e-298">サーバーは、圧縮されたコンテンツを送信するときに、 `Content-Encoding` 圧縮された応答のエンコード方法に関する情報をヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-298">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="8453e-299">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-299">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="8453e-300">`Accept-Encoding`ヘッダー値</span><span class="sxs-lookup"><span data-stu-id="8453e-300">`Accept-Encoding` header values</span></span> | <span data-ttu-id="8453e-301">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="8453e-301">Middleware Supported</span></span> | <span data-ttu-id="8453e-302">説明</span><span class="sxs-lookup"><span data-stu-id="8453e-302">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="8453e-303">あり (既定)</span><span class="sxs-lookup"><span data-stu-id="8453e-303">Yes (default)</span></span>        | [<span data-ttu-id="8453e-304">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="8453e-304">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="8453e-305">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-305">No</span></span>                   | [<span data-ttu-id="8453e-306">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="8453e-306">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="8453e-307">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-307">No</span></span>                   | [<span data-ttu-id="8453e-308">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="8453e-308">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="8453e-309">はい</span><span class="sxs-lookup"><span data-stu-id="8453e-309">Yes</span></span>                  | [<span data-ttu-id="8453e-310">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="8453e-310">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="8453e-311">はい</span><span class="sxs-lookup"><span data-stu-id="8453e-311">Yes</span></span>                  | <span data-ttu-id="8453e-312">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8453e-312">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="8453e-313">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-313">No</span></span>                   | [<span data-ttu-id="8453e-314">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="8453e-314">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="8453e-315">はい</span><span class="sxs-lookup"><span data-stu-id="8453e-315">Yes</span></span>                  | <span data-ttu-id="8453e-316">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="8453e-316">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="8453e-317">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-317">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="8453e-318">ミドルウェアを使用すると、カスタムヘッダー値の圧縮プロバイダーを追加でき `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-318">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="8453e-319">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-319">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="8453e-320">ミドルウェアは、 `q` 圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue,) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="8453e-320">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="8453e-321">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-321">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="8453e-322">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="8453e-322">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="8453e-323">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-323">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="8453e-324">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-324">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="8453e-325">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="8453e-325">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="8453e-326">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="8453e-326">Header</span></span>             | <span data-ttu-id="8453e-327">Role</span><span class="sxs-lookup"><span data-stu-id="8453e-327">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="8453e-328">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-328">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="8453e-329">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-329">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="8453e-330">圧縮が発生すると、 `Content-Length` 応答が圧縮されるときに本文の内容が変更されるため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-330">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="8453e-331">圧縮が行われると、 `Content-MD5` 本文の内容が変更され、ハッシュが有効でなくなったため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-331">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="8453e-332">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="8453e-332">Specifies the MIME type of the content.</span></span> <span data-ttu-id="8453e-333">すべての応答で、を指定する必要があり `Content-Type` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-333">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="8453e-334">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8453e-334">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="8453e-335">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="8453e-335">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="8453e-336">の値がであるサーバーからクライアントとプロキシに送信された場合 `Accept-Encoding` 、ヘッダーは、 `Vary` 要求のヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示し `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-336">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="8453e-337">ヘッダーを使用してコンテンツを返すと、 `Vary: Accept-Encoding` 圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="8453e-337">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="8453e-338">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="8453e-338">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="8453e-339">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-339">The sample illustrates:</span></span>

* <span data-ttu-id="8453e-340">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="8453e-340">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="8453e-341">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8453e-341">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="8453e-342">Package</span><span class="sxs-lookup"><span data-stu-id="8453e-342">Package</span></span>

<span data-ttu-id="8453e-343">ミドルウェアをプロジェクトに含めるには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8453e-343">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="8453e-344">構成</span><span class="sxs-lookup"><span data-stu-id="8453e-344">Configuration</span></span>

<span data-ttu-id="8453e-345">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="8453e-345">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="8453e-346">メモ:</span><span class="sxs-lookup"><span data-stu-id="8453e-346">Notes:</span></span>

* <span data-ttu-id="8453e-347">`app.UseResponseCompression`応答を圧縮するミドルウェアの前にを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-347">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="8453e-348">詳細については、「<xref:fundamentals/middleware/index#middleware-order>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-348">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="8453e-349">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用して要求ヘッダーを設定 `Accept-Encoding` し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="8453e-349">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="8453e-350">ヘッダーを使用せずにサンプルアプリに要求を送信 `Accept-Encoding` し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8453e-350">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="8453e-351">`Content-Encoding` `Vary` 応答にヘッダーとヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="8453e-351">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="8453e-354">ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信 `Accept-Encoding: br` し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8453e-354">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="8453e-355">`Content-Encoding` `Vary` 応答には、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="8453e-355">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="8453e-359">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-359">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="8453e-360">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-360">Brotli Compression Provider</span></span>

<span data-ttu-id="8453e-361"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="8453e-361">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="8453e-362">圧縮プロバイダーがに明示的に追加されていない場合は、次のように <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> なります。</span><span class="sxs-lookup"><span data-stu-id="8453e-362">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="8453e-363">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-363">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="8453e-364">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="8453e-364">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="8453e-365">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="8453e-365">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="8453e-366">圧縮プロバイダーが明示的に追加されている場合は、Brotli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-366">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="8453e-367">圧縮レベルをに設定 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> します。</span><span class="sxs-lookup"><span data-stu-id="8453e-367">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="8453e-368">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-368">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="8453e-369">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="8453e-369">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="8453e-370">Compression Level</span><span class="sxs-lookup"><span data-stu-id="8453e-370">Compression Level</span></span> | <span data-ttu-id="8453e-371">説明</span><span class="sxs-lookup"><span data-stu-id="8453e-371">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="8453e-372">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-372">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-373">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-373">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="8453e-374">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-374">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-375">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="8453e-375">No compression should be performed.</span></span> |
| [<span data-ttu-id="8453e-376">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-376">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-377">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-377">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a><span data-ttu-id="8453e-378">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-378">Gzip Compression Provider</span></span>

<span data-ttu-id="8453e-379">を使用して、 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="8453e-379">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="8453e-380">圧縮プロバイダーがに明示的に追加されていない場合は、次のように <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> なります。</span><span class="sxs-lookup"><span data-stu-id="8453e-380">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="8453e-381">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-381">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="8453e-382">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="8453e-382">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="8453e-383">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="8453e-383">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="8453e-384">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-384">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="8453e-385">圧縮レベルをに設定 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> します。</span><span class="sxs-lookup"><span data-stu-id="8453e-385">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="8453e-386">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-386">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="8453e-387">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="8453e-387">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="8453e-388">Compression Level</span><span class="sxs-lookup"><span data-stu-id="8453e-388">Compression Level</span></span> | <span data-ttu-id="8453e-389">説明</span><span class="sxs-lookup"><span data-stu-id="8453e-389">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="8453e-390">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-390">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-391">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-391">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="8453e-392">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-392">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-393">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="8453e-393">No compression should be performed.</span></span> |
| [<span data-ttu-id="8453e-394">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-394">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-395">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-395">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="8453e-396">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-396">Custom providers</span></span>

<span data-ttu-id="8453e-397">を使用して、カスタム圧縮実装を作成 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> します。</span><span class="sxs-lookup"><span data-stu-id="8453e-397">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="8453e-398">は、 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> このによって生成されるコンテンツエンコーディングを表し `ICompressionProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-398">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="8453e-399">ミドルウェアは、この情報を使用して、要求のヘッダーに指定されている一覧に基づいてプロバイダーを選択し `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-399">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="8453e-400">サンプルアプリを使用して、クライアントはヘッダーを使用して要求を送信し `Accept-Encoding: mycustomcompression` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-400">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="8453e-401">ミドルウェアはカスタム圧縮実装を使用して、応答をヘッダーと共に返し `Content-Encoding: mycustomcompression` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-401">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="8453e-402">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-402">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="8453e-403">ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認し `Accept-Encoding: mycustomcompression` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-403">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="8453e-404">`Vary` `Content-Encoding` 応答には、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="8453e-404">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="8453e-405">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="8453e-405">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="8453e-406">サンプルのクラスには圧縮実装がありません `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="8453e-406">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="8453e-407">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="8453e-407">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="8453e-410">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="8453e-410">MIME types</span></span>

<span data-ttu-id="8453e-411">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="8453e-411">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="8453e-412">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="8453e-412">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="8453e-413">などのワイルドカードの MIME 型はサポートされていないことに注意して `text/*` ください。</span><span class="sxs-lookup"><span data-stu-id="8453e-413">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="8453e-414">このサンプルアプリでは、の MIME の種類を追加 `image/svg+xml` し、ASP.NET Core バナーイメージ (*横断幕*) を圧縮して提供します。</span><span class="sxs-lookup"><span data-stu-id="8453e-414">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="8453e-415">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="8453e-415">Compression with secure protocol</span></span>

<span data-ttu-id="8453e-416">セキュリティで保護された接続での圧縮された応答は、 `EnableForHttps` 既定では無効になっているオプションを使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="8453e-416">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="8453e-417">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-417">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="8453e-418">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="8453e-418">Adding the Vary header</span></span>

<span data-ttu-id="8453e-419">ヘッダーに基づいて応答を圧縮する場合 `Accept-Encoding` 、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-419">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="8453e-420">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、 `Vary` ヘッダーに値を追加し `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-420">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="8453e-421">ASP.NET Core 2.0 以降では、 `Vary` 応答が圧縮されるときに、ミドルウェアによってヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-421">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="8453e-422">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="8453e-422">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="8453e-423">要求が Nginx によってプロキシされている場合、 `Accept-Encoding` ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-423">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="8453e-424">ヘッダーを削除すると、 `Accept-Encoding` ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="8453e-424">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="8453e-425">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-425">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="8453e-426">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-426">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="8453e-427">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="8453e-427">Working with IIS dynamic compression</span></span>

<span data-ttu-id="8453e-428">アプリケーションに対して無効にする、アクティブな IIS 動的圧縮モジュールがサーバーレベルで構成されている場合は、 *web.config*ファイルを追加してモジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="8453e-428">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="8453e-429">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-429">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8453e-430">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="8453e-430">Troubleshooting</span></span>

<span data-ttu-id="8453e-431">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用します。これにより、 `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="8453e-431">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="8453e-432">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="8453e-432">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="8453e-433">`Accept-Encoding`ヘッダーには `br` 、設定した `gzip` `*` カスタム圧縮プロバイダーに一致する値、、、またはカスタムエンコーディングが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8453e-433">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="8453e-434">値は、 `identity` または品質値 (qvalue, `q` ) の 0 (ゼロ) に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="8453e-434">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="8453e-435">MIME の種類 ( `Content-Type` ) を設定し、で構成されている mime の種類と一致させる必要があり <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-435">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="8453e-436">要求にヘッダーを含めることはできません `Content-Range` 。</span><span class="sxs-lookup"><span data-stu-id="8453e-436">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="8453e-437">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-437">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="8453e-438">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="8453e-438">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8453e-439">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8453e-439">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="8453e-440">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="8453e-440">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="8453e-441">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="8453e-441">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="8453e-442">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="8453e-442">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="8453e-443">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="8453e-443">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="8453e-444">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="8453e-444">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="8453e-445">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="8453e-445">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="8453e-446">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="8453e-446">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="8453e-447">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8453e-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="8453e-448">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="8453e-448">When to use Response Compression Middleware</span></span>

<span data-ttu-id="8453e-449">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="8453e-449">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="8453e-450">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-450">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="8453e-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) Server と[kestrel](xref:fundamentals/servers/kestrel) server では、現在、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="8453e-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="8453e-452">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="8453e-452">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="8453e-453">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="8453e-453">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="8453e-454">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="8453e-454">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="8453e-455">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="8453e-455">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="8453e-456">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="8453e-456">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="8453e-457">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="8453e-457">Hosting directly on:</span></span>
  * <span data-ttu-id="8453e-458">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="8453e-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="8453e-459">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="8453e-459">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="8453e-460">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="8453e-460">Response compression</span></span>

<span data-ttu-id="8453e-461">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="8453e-461">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="8453e-462">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8453e-462">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="8453e-463">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="8453e-463">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="8453e-464">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="8453e-464">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="8453e-465">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="8453e-465">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="8453e-466">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-466">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="8453e-467">クライアントは、圧縮されたコンテンツを処理できる場合、 `Accept-Encoding` 要求と共にヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-467">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="8453e-468">サーバーは、圧縮されたコンテンツを送信するときに、 `Content-Encoding` 圧縮された応答のエンコード方法に関する情報をヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-468">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="8453e-469">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-469">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="8453e-470">`Accept-Encoding`ヘッダー値</span><span class="sxs-lookup"><span data-stu-id="8453e-470">`Accept-Encoding` header values</span></span> | <span data-ttu-id="8453e-471">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="8453e-471">Middleware Supported</span></span> | <span data-ttu-id="8453e-472">説明</span><span class="sxs-lookup"><span data-stu-id="8453e-472">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="8453e-473">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-473">No</span></span>                   | [<span data-ttu-id="8453e-474">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="8453e-474">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="8453e-475">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-475">No</span></span>                   | [<span data-ttu-id="8453e-476">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="8453e-476">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="8453e-477">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-477">No</span></span>                   | [<span data-ttu-id="8453e-478">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="8453e-478">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="8453e-479">あり (既定)</span><span class="sxs-lookup"><span data-stu-id="8453e-479">Yes (default)</span></span>        | [<span data-ttu-id="8453e-480">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="8453e-480">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="8453e-481">はい</span><span class="sxs-lookup"><span data-stu-id="8453e-481">Yes</span></span>                  | <span data-ttu-id="8453e-482">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8453e-482">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="8453e-483">いいえ</span><span class="sxs-lookup"><span data-stu-id="8453e-483">No</span></span>                   | [<span data-ttu-id="8453e-484">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="8453e-484">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="8453e-485">はい</span><span class="sxs-lookup"><span data-stu-id="8453e-485">Yes</span></span>                  | <span data-ttu-id="8453e-486">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="8453e-486">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="8453e-487">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-487">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="8453e-488">ミドルウェアを使用すると、カスタムヘッダー値の圧縮プロバイダーを追加でき `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-488">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="8453e-489">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-489">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="8453e-490">ミドルウェアは、 `q` 圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue,) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="8453e-490">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="8453e-491">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-491">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="8453e-492">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="8453e-492">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="8453e-493">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-493">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="8453e-494">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-494">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="8453e-495">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="8453e-495">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="8453e-496">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="8453e-496">Header</span></span>             | <span data-ttu-id="8453e-497">Role</span><span class="sxs-lookup"><span data-stu-id="8453e-497">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="8453e-498">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-498">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="8453e-499">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-499">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="8453e-500">圧縮が発生すると、 `Content-Length` 応答が圧縮されるときに本文の内容が変更されるため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-500">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="8453e-501">圧縮が行われると、 `Content-MD5` 本文の内容が変更され、ハッシュが有効でなくなったため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-501">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="8453e-502">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="8453e-502">Specifies the MIME type of the content.</span></span> <span data-ttu-id="8453e-503">すべての応答で、を指定する必要があり `Content-Type` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-503">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="8453e-504">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8453e-504">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="8453e-505">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="8453e-505">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="8453e-506">の値がであるサーバーからクライアントとプロキシに送信された場合 `Accept-Encoding` 、ヘッダーは、 `Vary` 要求のヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示し `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-506">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="8453e-507">ヘッダーを使用してコンテンツを返すと、 `Vary: Accept-Encoding` 圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="8453e-507">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="8453e-508">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="8453e-508">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="8453e-509">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="8453e-509">The sample illustrates:</span></span>

* <span data-ttu-id="8453e-510">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="8453e-510">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="8453e-511">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8453e-511">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="8453e-512">Package</span><span class="sxs-lookup"><span data-stu-id="8453e-512">Package</span></span>

<span data-ttu-id="8453e-513">ミドルウェアをプロジェクトに含めるには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8453e-513">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="8453e-514">構成</span><span class="sxs-lookup"><span data-stu-id="8453e-514">Configuration</span></span>

<span data-ttu-id="8453e-515">次のコードは、既定の MIME の種類と[Gzip 圧縮プロバイダー](#gzip-compression-provider)の応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="8453e-515">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="8453e-516">メモ:</span><span class="sxs-lookup"><span data-stu-id="8453e-516">Notes:</span></span>

* <span data-ttu-id="8453e-517">`app.UseResponseCompression`応答を圧縮するミドルウェアの前にを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-517">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="8453e-518">詳細については、「<xref:fundamentals/middleware/index#middleware-order>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-518">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="8453e-519">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用して要求ヘッダーを設定 `Accept-Encoding` し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="8453e-519">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="8453e-520">ヘッダーを使用せずにサンプルアプリに要求を送信 `Accept-Encoding` し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8453e-520">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="8453e-521">`Content-Encoding` `Vary` 応答にヘッダーとヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="8453e-521">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="8453e-524">ヘッダーを使用してサンプルアプリに要求を送信 `Accept-Encoding: gzip` し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8453e-524">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="8453e-525">`Content-Encoding` `Vary` 応答には、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="8453e-525">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと gzip の値で表示されます。](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="8453e-529">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-529">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="8453e-530">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-530">Gzip Compression Provider</span></span>

<span data-ttu-id="8453e-531">を使用して、 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="8453e-531">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="8453e-532">圧縮プロバイダーがに明示的に追加されていない場合は、次のように <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> なります。</span><span class="sxs-lookup"><span data-stu-id="8453e-532">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="8453e-533">Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-533">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="8453e-534">クライアントで Gzip 圧縮がサポートされている場合、圧縮の既定値は Gzip です。</span><span class="sxs-lookup"><span data-stu-id="8453e-534">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="8453e-535">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-535">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="8453e-536">圧縮レベルをに設定 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> します。</span><span class="sxs-lookup"><span data-stu-id="8453e-536">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="8453e-537">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-537">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="8453e-538">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="8453e-538">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="8453e-539">Compression Level</span><span class="sxs-lookup"><span data-stu-id="8453e-539">Compression Level</span></span> | <span data-ttu-id="8453e-540">説明</span><span class="sxs-lookup"><span data-stu-id="8453e-540">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="8453e-541">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-541">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-542">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-542">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="8453e-543">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-543">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-544">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="8453e-544">No compression should be performed.</span></span> |
| [<span data-ttu-id="8453e-545">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="8453e-545">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="8453e-546">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-546">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="8453e-547">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8453e-547">Custom providers</span></span>

<span data-ttu-id="8453e-548">を使用して、カスタム圧縮実装を作成 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> します。</span><span class="sxs-lookup"><span data-stu-id="8453e-548">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="8453e-549">は、 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> このによって生成されるコンテンツエンコーディングを表し `ICompressionProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-549">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="8453e-550">ミドルウェアは、この情報を使用して、要求のヘッダーに指定されている一覧に基づいてプロバイダーを選択し `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-550">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="8453e-551">サンプルアプリを使用して、クライアントはヘッダーを使用して要求を送信し `Accept-Encoding: mycustomcompression` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-551">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="8453e-552">ミドルウェアはカスタム圧縮実装を使用して、応答をヘッダーと共に返し `Content-Encoding: mycustomcompression` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-552">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="8453e-553">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-553">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="8453e-554">ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認し `Accept-Encoding: mycustomcompression` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-554">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="8453e-555">`Vary` `Content-Encoding` 応答には、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="8453e-555">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="8453e-556">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="8453e-556">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="8453e-557">サンプルのクラスには圧縮実装がありません `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="8453e-557">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="8453e-558">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="8453e-558">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="8453e-561">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="8453e-561">MIME types</span></span>

<span data-ttu-id="8453e-562">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="8453e-562">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="8453e-563">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="8453e-563">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="8453e-564">などのワイルドカードの MIME 型はサポートされていないことに注意して `text/*` ください。</span><span class="sxs-lookup"><span data-stu-id="8453e-564">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="8453e-565">このサンプルアプリでは、の MIME の種類を追加 `image/svg+xml` し、ASP.NET Core バナーイメージ (*横断幕*) を圧縮して提供します。</span><span class="sxs-lookup"><span data-stu-id="8453e-565">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="8453e-566">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="8453e-566">Compression with secure protocol</span></span>

<span data-ttu-id="8453e-567">セキュリティで保護された接続での圧縮された応答は、 `EnableForHttps` 既定では無効になっているオプションを使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="8453e-567">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="8453e-568">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-568">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="8453e-569">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="8453e-569">Adding the Vary header</span></span>

<span data-ttu-id="8453e-570">ヘッダーに基づいて応答を圧縮する場合 `Accept-Encoding` 、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-570">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="8453e-571">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、 `Vary` ヘッダーに値を追加し `Accept-Encoding` ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-571">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="8453e-572">ASP.NET Core 2.0 以降では、 `Vary` 応答が圧縮されるときに、ミドルウェアによってヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-572">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="8453e-573">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="8453e-573">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="8453e-574">要求が Nginx によってプロキシされている場合、 `Accept-Encoding` ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-574">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="8453e-575">ヘッダーを削除すると、 `Accept-Encoding` ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="8453e-575">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="8453e-576">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-576">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="8453e-577">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8453e-577">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="8453e-578">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="8453e-578">Working with IIS dynamic compression</span></span>

<span data-ttu-id="8453e-579">アプリケーションに対して無効にする、アクティブな IIS 動的圧縮モジュールがサーバーレベルで構成されている場合は、 *web.config*ファイルを追加してモジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="8453e-579">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="8453e-580">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8453e-580">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8453e-581">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="8453e-581">Troubleshooting</span></span>

<span data-ttu-id="8453e-582">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用します。これにより、 `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="8453e-582">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="8453e-583">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="8453e-583">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="8453e-584">`Accept-Encoding`ヘッダーには `gzip` 、設定した `*` カスタム圧縮プロバイダーに一致する値、、またはカスタムエンコーディングが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8453e-584">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="8453e-585">値は、 `identity` または品質値 (qvalue, `q` ) の 0 (ゼロ) に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="8453e-585">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="8453e-586">MIME の種類 ( `Content-Type` ) を設定し、で構成されている mime の種類と一致させる必要があり <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="8453e-586">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="8453e-587">要求にヘッダーを含めることはできません `Content-Range` 。</span><span class="sxs-lookup"><span data-stu-id="8453e-587">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="8453e-588">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8453e-588">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="8453e-589">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="8453e-589">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8453e-590">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8453e-590">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="8453e-591">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="8453e-591">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="8453e-592">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="8453e-592">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="8453e-593">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="8453e-593">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="8453e-594">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="8453e-594">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
