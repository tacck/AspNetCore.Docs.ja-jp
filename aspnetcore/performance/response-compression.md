---
title: ASP.NET Core での応答圧縮
author: rick-anderson
description: 応答圧縮と ASP.NET Core アプリで応答圧縮ミドルウェアを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/response-compression
ms.openlocfilehash: 8fc68f2303bfcf16d279b829ab9441a80119f1bb
ms.sourcegitcommit: 755952496316fdb0923689109b536b609ce525ee
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82643080"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="f6ea0-103">ASP.NET Core での応答圧縮</span><span class="sxs-lookup"><span data-stu-id="f6ea0-103">Response compression in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6ea0-104">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-104">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="f6ea0-105">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-105">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="f6ea0-106">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-106">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="f6ea0-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="f6ea0-108">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="f6ea0-108">When to use Response Compression Middleware</span></span>

<span data-ttu-id="f6ea0-109">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-109">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="f6ea0-110">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-110">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="f6ea0-111">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="f6ea0-112">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-112">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="f6ea0-113">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-113">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="f6ea0-114">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="f6ea0-114">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="f6ea0-115">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="f6ea0-115">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="f6ea0-116">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="f6ea0-116">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="f6ea0-117">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="f6ea0-117">Hosting directly on:</span></span>
  * <span data-ttu-id="f6ea0-118">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="f6ea0-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="f6ea0-119">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-119">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="f6ea0-120">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="f6ea0-120">Response compression</span></span>

<span data-ttu-id="f6ea0-121">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-121">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="f6ea0-122">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-122">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="f6ea0-123">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-123">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="f6ea0-124">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-124">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="f6ea0-125">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-125">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="f6ea0-126">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-126">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="f6ea0-127">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に`Accept-Encoding`ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-127">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="f6ea0-128">サーバーは、圧縮されたコンテンツを送信するときに`Content-Encoding` 、圧縮された応答のエンコード方法に関する情報をヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-128">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="f6ea0-129">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-129">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="f6ea0-130">`Accept-Encoding`ヘッダー値</span><span class="sxs-lookup"><span data-stu-id="f6ea0-130">`Accept-Encoding` header values</span></span> | <span data-ttu-id="f6ea0-131">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f6ea0-131">Middleware Supported</span></span> | <span data-ttu-id="f6ea0-132">[説明]</span><span class="sxs-lookup"><span data-stu-id="f6ea0-132">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="f6ea0-133">あり (既定)</span><span class="sxs-lookup"><span data-stu-id="f6ea0-133">Yes (default)</span></span>        | [<span data-ttu-id="f6ea0-134">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="f6ea0-134">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="f6ea0-135">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-135">No</span></span>                   | [<span data-ttu-id="f6ea0-136">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="f6ea0-136">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="f6ea0-137">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-137">No</span></span>                   | [<span data-ttu-id="f6ea0-138">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-138">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="f6ea0-139">はい</span><span class="sxs-lookup"><span data-stu-id="f6ea0-139">Yes</span></span>                  | [<span data-ttu-id="f6ea0-140">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="f6ea0-140">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="f6ea0-141">はい</span><span class="sxs-lookup"><span data-stu-id="f6ea0-141">Yes</span></span>                  | <span data-ttu-id="f6ea0-142">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-142">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="f6ea0-143">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-143">No</span></span>                   | [<span data-ttu-id="f6ea0-144">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="f6ea0-144">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="f6ea0-145">はい</span><span class="sxs-lookup"><span data-stu-id="f6ea0-145">Yes</span></span>                  | <span data-ttu-id="f6ea0-146">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="f6ea0-146">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="f6ea0-147">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-147">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="f6ea0-148">ミドルウェアを使用すると、カスタム`Accept-Encoding`ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-148">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="f6ea0-149">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-149">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="f6ea0-150">ミドルウェアは、圧縮スキームの優先順位を決定するために`q`クライアントから送信されるときに、品質の値 (qvalue,) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-150">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="f6ea0-151">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-151">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="f6ea0-152">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-152">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="f6ea0-153">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-153">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="f6ea0-154">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-154">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="f6ea0-155">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-155">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="f6ea0-156">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-156">Header</span></span>             | <span data-ttu-id="f6ea0-157">Role</span><span class="sxs-lookup"><span data-stu-id="f6ea0-157">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="f6ea0-158">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-158">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="f6ea0-159">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-159">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="f6ea0-160">圧縮が発生すると`Content-Length` 、応答が圧縮されるときに本文の内容が変更されるため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-160">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="f6ea0-161">圧縮が行われると`Content-MD5` 、本文の内容が変更され、ハッシュが有効でなくなったため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-161">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="f6ea0-162">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-162">Specifies the MIME type of the content.</span></span> <span data-ttu-id="f6ea0-163">すべての応答で、 `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-163">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="f6ea0-164">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-164">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="f6ea0-165">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-165">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="f6ea0-166">の`Accept-Encoding`値がであるサーバーからクライアントとプロキシに送信された`Vary`場合、ヘッダーは、要求の`Accept-Encoding`ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-166">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="f6ea0-167">`Vary: Accept-Encoding`ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-167">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="f6ea0-168">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-168">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="f6ea0-169">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-169">The sample illustrates:</span></span>

* <span data-ttu-id="f6ea0-170">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-170">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="f6ea0-171">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-171">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="f6ea0-172">Package</span><span class="sxs-lookup"><span data-stu-id="f6ea0-172">Package</span></span>

<span data-ttu-id="f6ea0-173">応答圧縮ミドルウェアは、AspNetCore アプリ ASP.NET Core に暗黙的に含まれる[ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-173">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="f6ea0-174">構成</span><span class="sxs-lookup"><span data-stu-id="f6ea0-174">Configuration</span></span>

<span data-ttu-id="f6ea0-175">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-175">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="f6ea0-176">メモ:</span><span class="sxs-lookup"><span data-stu-id="f6ea0-176">Notes:</span></span>

* <span data-ttu-id="f6ea0-177">`app.UseResponseCompression`応答を圧縮するミドルウェアの前にを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-177">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="f6ea0-178">詳細については、「<xref:fundamentals/middleware/index#middleware-order>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-178">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="f6ea0-179">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用して`Accept-Encoding`要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-179">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="f6ea0-180">ヘッダーを`Accept-Encoding`使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-180">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="f6ea0-181">応答`Content-Encoding`に`Vary`ヘッダーとヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-181">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="f6ea0-184">`Accept-Encoding: br`ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-184">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="f6ea0-185">応答`Content-Encoding`に`Vary`は、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-185">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="f6ea0-189">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-189">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="f6ea0-190">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-190">Brotli Compression Provider</span></span>

<span data-ttu-id="f6ea0-191"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-191">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="f6ea0-192">圧縮プロバイダーがに明示的に追加さ<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>れていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-192">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="f6ea0-193">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-193">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="f6ea0-194">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-194">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="f6ea0-195">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-195">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="f6ea0-196">圧縮プロバイダーが明示的に追加されている場合は、Brotli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-196">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="f6ea0-197">圧縮レベルをに<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>設定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-197">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="f6ea0-198">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-198">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="f6ea0-199">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-199">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="f6ea0-200">Compression Level</span><span class="sxs-lookup"><span data-stu-id="f6ea0-200">Compression Level</span></span> | <span data-ttu-id="f6ea0-201">[説明]</span><span class="sxs-lookup"><span data-stu-id="f6ea0-201">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="f6ea0-202">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-202">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-203">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-203">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="f6ea0-204">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-204">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-205">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-205">No compression should be performed.</span></span> |
| [<span data-ttu-id="f6ea0-206">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-206">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-207">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-207">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="f6ea0-208">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-208">Gzip Compression Provider</span></span>

<span data-ttu-id="f6ea0-209">を使用<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-209">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="f6ea0-210">圧縮プロバイダーがに明示的に追加さ<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>れていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-210">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="f6ea0-211">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-211">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="f6ea0-212">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-212">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="f6ea0-213">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-213">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="f6ea0-214">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-214">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="f6ea0-215">圧縮レベルをに<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>設定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-215">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="f6ea0-216">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-216">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="f6ea0-217">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-217">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="f6ea0-218">Compression Level</span><span class="sxs-lookup"><span data-stu-id="f6ea0-218">Compression Level</span></span> | <span data-ttu-id="f6ea0-219">[説明]</span><span class="sxs-lookup"><span data-stu-id="f6ea0-219">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="f6ea0-220">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-220">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-221">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-221">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="f6ea0-222">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-222">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-223">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-223">No compression should be performed.</span></span> |
| [<span data-ttu-id="f6ea0-224">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-224">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-225">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-225">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="f6ea0-226">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-226">Custom providers</span></span>

<span data-ttu-id="f6ea0-227">を使用して、 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-227">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="f6ea0-228">は<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 、この`ICompressionProvider`によって生成されるコンテンツエンコーディングを表します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-228">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="f6ea0-229">ミドルウェアは、この情報を使用して、要求の`Accept-Encoding`ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-229">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="f6ea0-230">サンプルアプリを使用して、クライアントは`Accept-Encoding: mycustomcompression`ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-230">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f6ea0-231">ミドルウェアはカスタム圧縮実装を使用して、応答を`Content-Encoding: mycustomcompression`ヘッダーと共に返します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-231">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f6ea0-232">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-232">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="f6ea0-233">`Accept-Encoding: mycustomcompression`ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-233">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="f6ea0-234">応答`Vary`に`Content-Encoding`は、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-234">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="f6ea0-235">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-235">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="f6ea0-236">サンプルの`CustomCompressionProvider`クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-236">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="f6ea0-237">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-237">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="f6ea0-240">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-240">MIME types</span></span>

<span data-ttu-id="f6ea0-241">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-241">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="f6ea0-242">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-242">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="f6ea0-243">などのワイルドカードの MIME 型は`text/*`サポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-243">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="f6ea0-244">このサンプルアプリでは、の MIME `image/svg+xml`の種類を追加し、ASP.NET Core バナーイメージ (*横断幕*) を圧縮して提供します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-244">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="f6ea0-245">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="f6ea0-245">Compression with secure protocol</span></span>

<span data-ttu-id="f6ea0-246">セキュリティで保護された接続での圧縮`EnableForHttps`された応答は、既定では無効になっているオプションを使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-246">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="f6ea0-247">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-247">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="f6ea0-248">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="f6ea0-248">Adding the Vary header</span></span>

<span data-ttu-id="f6ea0-249">`Accept-Encoding`ヘッダーに基づいて応答を圧縮する場合、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-249">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="f6ea0-250">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシ`Vary`キャッシュに指示するため`Accept-Encoding`に、ヘッダーに値を追加します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-250">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="f6ea0-251">ASP.NET Core 2.0 以降では、応答が圧縮さ`Vary`れるときに、ミドルウェアによってヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-251">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="f6ea0-252">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="f6ea0-252">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="f6ea0-253">要求が Nginx によってプロキシされ`Accept-Encoding`ている場合、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-253">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="f6ea0-254">`Accept-Encoding`ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-254">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="f6ea0-255">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-255">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="f6ea0-256">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-256">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="f6ea0-257">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="f6ea0-257">Working with IIS dynamic compression</span></span>

<span data-ttu-id="f6ea0-258">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-258">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="f6ea0-259">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-259">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f6ea0-260">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="f6ea0-260">Troubleshooting</span></span>

<span data-ttu-id="f6ea0-261">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用します。これにより、 `Accept-Encoding`要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-261">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="f6ea0-262">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-262">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="f6ea0-263">ヘッダー `Accept-Encoding`には`br`、設定したカスタム圧縮`gzip`プロバイダー `*`に一致する値、、、またはカスタムエンコーディングが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-263">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="f6ea0-264">値は、 `identity`または品質値 (qvalue, `q`) の 0 (ゼロ) に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-264">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="f6ea0-265">MIME の種類 (`Content-Type`) を設定し、で構成されている mime の<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-265">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="f6ea0-266">要求にヘッダーを`Content-Range`含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-266">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="f6ea0-267">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-267">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="f6ea0-268">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="f6ea0-268">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6ea0-269">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f6ea0-269">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="f6ea0-270">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="f6ea0-270">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="f6ea0-271">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="f6ea0-271">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="f6ea0-272">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="f6ea0-272">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="f6ea0-273">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="f6ea0-273">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f6ea0-274">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-274">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="f6ea0-275">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-275">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="f6ea0-276">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-276">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="f6ea0-277">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="f6ea0-278">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="f6ea0-278">When to use Response Compression Middleware</span></span>

<span data-ttu-id="f6ea0-279">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-279">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="f6ea0-280">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-280">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="f6ea0-281">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="f6ea0-282">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-282">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="f6ea0-283">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-283">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="f6ea0-284">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="f6ea0-284">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="f6ea0-285">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="f6ea0-285">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="f6ea0-286">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="f6ea0-286">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="f6ea0-287">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="f6ea0-287">Hosting directly on:</span></span>
  * <span data-ttu-id="f6ea0-288">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="f6ea0-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="f6ea0-289">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-289">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="f6ea0-290">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="f6ea0-290">Response compression</span></span>

<span data-ttu-id="f6ea0-291">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-291">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="f6ea0-292">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-292">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="f6ea0-293">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-293">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="f6ea0-294">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-294">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="f6ea0-295">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-295">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="f6ea0-296">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-296">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="f6ea0-297">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に`Accept-Encoding`ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-297">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="f6ea0-298">サーバーは、圧縮されたコンテンツを送信するときに`Content-Encoding` 、圧縮された応答のエンコード方法に関する情報をヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-298">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="f6ea0-299">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-299">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="f6ea0-300">`Accept-Encoding`ヘッダー値</span><span class="sxs-lookup"><span data-stu-id="f6ea0-300">`Accept-Encoding` header values</span></span> | <span data-ttu-id="f6ea0-301">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f6ea0-301">Middleware Supported</span></span> | <span data-ttu-id="f6ea0-302">[説明]</span><span class="sxs-lookup"><span data-stu-id="f6ea0-302">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="f6ea0-303">あり (既定)</span><span class="sxs-lookup"><span data-stu-id="f6ea0-303">Yes (default)</span></span>        | [<span data-ttu-id="f6ea0-304">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="f6ea0-304">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="f6ea0-305">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-305">No</span></span>                   | [<span data-ttu-id="f6ea0-306">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="f6ea0-306">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="f6ea0-307">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-307">No</span></span>                   | [<span data-ttu-id="f6ea0-308">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-308">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="f6ea0-309">はい</span><span class="sxs-lookup"><span data-stu-id="f6ea0-309">Yes</span></span>                  | [<span data-ttu-id="f6ea0-310">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="f6ea0-310">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="f6ea0-311">はい</span><span class="sxs-lookup"><span data-stu-id="f6ea0-311">Yes</span></span>                  | <span data-ttu-id="f6ea0-312">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-312">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="f6ea0-313">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-313">No</span></span>                   | [<span data-ttu-id="f6ea0-314">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="f6ea0-314">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="f6ea0-315">はい</span><span class="sxs-lookup"><span data-stu-id="f6ea0-315">Yes</span></span>                  | <span data-ttu-id="f6ea0-316">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="f6ea0-316">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="f6ea0-317">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-317">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="f6ea0-318">ミドルウェアを使用すると、カスタム`Accept-Encoding`ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-318">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="f6ea0-319">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-319">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="f6ea0-320">ミドルウェアは、圧縮スキームの優先順位を決定するために`q`クライアントから送信されるときに、品質の値 (qvalue,) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-320">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="f6ea0-321">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-321">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="f6ea0-322">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-322">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="f6ea0-323">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-323">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="f6ea0-324">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-324">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="f6ea0-325">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-325">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="f6ea0-326">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-326">Header</span></span>             | <span data-ttu-id="f6ea0-327">Role</span><span class="sxs-lookup"><span data-stu-id="f6ea0-327">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="f6ea0-328">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-328">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="f6ea0-329">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-329">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="f6ea0-330">圧縮が発生すると`Content-Length` 、応答が圧縮されるときに本文の内容が変更されるため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-330">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="f6ea0-331">圧縮が行われると`Content-MD5` 、本文の内容が変更され、ハッシュが有効でなくなったため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-331">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="f6ea0-332">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-332">Specifies the MIME type of the content.</span></span> <span data-ttu-id="f6ea0-333">すべての応答で、 `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-333">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="f6ea0-334">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-334">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="f6ea0-335">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-335">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="f6ea0-336">の`Accept-Encoding`値がであるサーバーからクライアントとプロキシに送信された`Vary`場合、ヘッダーは、要求の`Accept-Encoding`ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-336">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="f6ea0-337">`Vary: Accept-Encoding`ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-337">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="f6ea0-338">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-338">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="f6ea0-339">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-339">The sample illustrates:</span></span>

* <span data-ttu-id="f6ea0-340">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-340">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="f6ea0-341">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-341">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="f6ea0-342">Package</span><span class="sxs-lookup"><span data-stu-id="f6ea0-342">Package</span></span>

<span data-ttu-id="f6ea0-343">ミドルウェアをプロジェクトに含めるには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-343">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="f6ea0-344">構成</span><span class="sxs-lookup"><span data-stu-id="f6ea0-344">Configuration</span></span>

<span data-ttu-id="f6ea0-345">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-345">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="f6ea0-346">メモ:</span><span class="sxs-lookup"><span data-stu-id="f6ea0-346">Notes:</span></span>

* <span data-ttu-id="f6ea0-347">`app.UseResponseCompression`応答を圧縮するミドルウェアの前にを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-347">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="f6ea0-348">詳細については、「<xref:fundamentals/middleware/index#middleware-order>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-348">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="f6ea0-349">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用して`Accept-Encoding`要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-349">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="f6ea0-350">ヘッダーを`Accept-Encoding`使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-350">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="f6ea0-351">応答`Content-Encoding`に`Vary`ヘッダーとヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-351">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="f6ea0-354">`Accept-Encoding: br`ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-354">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="f6ea0-355">応答`Content-Encoding`に`Vary`は、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-355">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="f6ea0-359">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-359">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="f6ea0-360">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-360">Brotli Compression Provider</span></span>

<span data-ttu-id="f6ea0-361"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-361">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="f6ea0-362">圧縮プロバイダーがに明示的に追加さ<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>れていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-362">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="f6ea0-363">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-363">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="f6ea0-364">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-364">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="f6ea0-365">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-365">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="f6ea0-366">圧縮プロバイダーが明示的に追加されている場合は、Brotli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-366">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="f6ea0-367">圧縮レベルをに<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>設定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-367">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="f6ea0-368">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-368">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="f6ea0-369">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-369">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="f6ea0-370">Compression Level</span><span class="sxs-lookup"><span data-stu-id="f6ea0-370">Compression Level</span></span> | <span data-ttu-id="f6ea0-371">[説明]</span><span class="sxs-lookup"><span data-stu-id="f6ea0-371">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="f6ea0-372">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-372">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-373">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-373">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="f6ea0-374">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-374">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-375">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-375">No compression should be performed.</span></span> |
| [<span data-ttu-id="f6ea0-376">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-376">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-377">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-377">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="f6ea0-378">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-378">Gzip Compression Provider</span></span>

<span data-ttu-id="f6ea0-379">を使用<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-379">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="f6ea0-380">圧縮プロバイダーがに明示的に追加さ<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>れていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-380">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="f6ea0-381">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-381">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="f6ea0-382">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-382">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="f6ea0-383">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-383">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="f6ea0-384">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-384">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="f6ea0-385">圧縮レベルをに<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>設定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-385">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="f6ea0-386">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-386">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="f6ea0-387">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-387">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="f6ea0-388">Compression Level</span><span class="sxs-lookup"><span data-stu-id="f6ea0-388">Compression Level</span></span> | <span data-ttu-id="f6ea0-389">[説明]</span><span class="sxs-lookup"><span data-stu-id="f6ea0-389">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="f6ea0-390">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-390">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-391">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-391">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="f6ea0-392">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-392">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-393">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-393">No compression should be performed.</span></span> |
| [<span data-ttu-id="f6ea0-394">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-394">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-395">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-395">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="f6ea0-396">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-396">Custom providers</span></span>

<span data-ttu-id="f6ea0-397">を使用して、 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-397">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="f6ea0-398">は<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 、この`ICompressionProvider`によって生成されるコンテンツエンコーディングを表します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-398">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="f6ea0-399">ミドルウェアは、この情報を使用して、要求の`Accept-Encoding`ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-399">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="f6ea0-400">サンプルアプリを使用して、クライアントは`Accept-Encoding: mycustomcompression`ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-400">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f6ea0-401">ミドルウェアはカスタム圧縮実装を使用して、応答を`Content-Encoding: mycustomcompression`ヘッダーと共に返します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-401">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f6ea0-402">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-402">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="f6ea0-403">`Accept-Encoding: mycustomcompression`ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-403">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="f6ea0-404">応答`Vary`に`Content-Encoding`は、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-404">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="f6ea0-405">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-405">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="f6ea0-406">サンプルの`CustomCompressionProvider`クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-406">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="f6ea0-407">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-407">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="f6ea0-410">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-410">MIME types</span></span>

<span data-ttu-id="f6ea0-411">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-411">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="f6ea0-412">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-412">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="f6ea0-413">などのワイルドカードの MIME 型は`text/*`サポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-413">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="f6ea0-414">このサンプルアプリでは、の MIME `image/svg+xml`の種類を追加し、ASP.NET Core バナーイメージ (*横断幕*) を圧縮して提供します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-414">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="f6ea0-415">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="f6ea0-415">Compression with secure protocol</span></span>

<span data-ttu-id="f6ea0-416">セキュリティで保護された接続での圧縮`EnableForHttps`された応答は、既定では無効になっているオプションを使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-416">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="f6ea0-417">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-417">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="f6ea0-418">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="f6ea0-418">Adding the Vary header</span></span>

<span data-ttu-id="f6ea0-419">`Accept-Encoding`ヘッダーに基づいて応答を圧縮する場合、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-419">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="f6ea0-420">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシ`Vary`キャッシュに指示するため`Accept-Encoding`に、ヘッダーに値を追加します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-420">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="f6ea0-421">ASP.NET Core 2.0 以降では、応答が圧縮さ`Vary`れるときに、ミドルウェアによってヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-421">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="f6ea0-422">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="f6ea0-422">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="f6ea0-423">要求が Nginx によってプロキシされ`Accept-Encoding`ている場合、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-423">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="f6ea0-424">`Accept-Encoding`ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-424">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="f6ea0-425">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-425">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="f6ea0-426">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-426">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="f6ea0-427">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="f6ea0-427">Working with IIS dynamic compression</span></span>

<span data-ttu-id="f6ea0-428">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-428">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="f6ea0-429">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-429">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f6ea0-430">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="f6ea0-430">Troubleshooting</span></span>

<span data-ttu-id="f6ea0-431">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用します。これにより、 `Accept-Encoding`要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-431">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="f6ea0-432">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-432">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="f6ea0-433">ヘッダー `Accept-Encoding`には`br`、設定したカスタム圧縮`gzip`プロバイダー `*`に一致する値、、、またはカスタムエンコーディングが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-433">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="f6ea0-434">値は、 `identity`または品質値 (qvalue, `q`) の 0 (ゼロ) に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-434">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="f6ea0-435">MIME の種類 (`Content-Type`) を設定し、で構成されている mime の<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-435">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="f6ea0-436">要求にヘッダーを`Content-Range`含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-436">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="f6ea0-437">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-437">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="f6ea0-438">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="f6ea0-438">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6ea0-439">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f6ea0-439">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="f6ea0-440">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="f6ea0-440">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="f6ea0-441">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="f6ea0-441">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="f6ea0-442">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="f6ea0-442">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="f6ea0-443">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="f6ea0-443">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f6ea0-444">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-444">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="f6ea0-445">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-445">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="f6ea0-446">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-446">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="f6ea0-447">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="f6ea0-448">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="f6ea0-448">When to use Response Compression Middleware</span></span>

<span data-ttu-id="f6ea0-449">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-449">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="f6ea0-450">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-450">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="f6ea0-451">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="f6ea0-452">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-452">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="f6ea0-453">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-453">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="f6ea0-454">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="f6ea0-454">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="f6ea0-455">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="f6ea0-455">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="f6ea0-456">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="f6ea0-456">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="f6ea0-457">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="f6ea0-457">Hosting directly on:</span></span>
  * <span data-ttu-id="f6ea0-458">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="f6ea0-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="f6ea0-459">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-459">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="f6ea0-460">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="f6ea0-460">Response compression</span></span>

<span data-ttu-id="f6ea0-461">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-461">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="f6ea0-462">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-462">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="f6ea0-463">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-463">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="f6ea0-464">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-464">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="f6ea0-465">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-465">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="f6ea0-466">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-466">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="f6ea0-467">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に`Accept-Encoding`ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-467">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="f6ea0-468">サーバーは、圧縮されたコンテンツを送信するときに`Content-Encoding` 、圧縮された応答のエンコード方法に関する情報をヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-468">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="f6ea0-469">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-469">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="f6ea0-470">`Accept-Encoding`ヘッダー値</span><span class="sxs-lookup"><span data-stu-id="f6ea0-470">`Accept-Encoding` header values</span></span> | <span data-ttu-id="f6ea0-471">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f6ea0-471">Middleware Supported</span></span> | <span data-ttu-id="f6ea0-472">[説明]</span><span class="sxs-lookup"><span data-stu-id="f6ea0-472">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="f6ea0-473">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-473">No</span></span>                   | [<span data-ttu-id="f6ea0-474">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="f6ea0-474">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="f6ea0-475">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-475">No</span></span>                   | [<span data-ttu-id="f6ea0-476">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="f6ea0-476">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="f6ea0-477">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-477">No</span></span>                   | [<span data-ttu-id="f6ea0-478">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-478">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="f6ea0-479">あり (既定)</span><span class="sxs-lookup"><span data-stu-id="f6ea0-479">Yes (default)</span></span>        | [<span data-ttu-id="f6ea0-480">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="f6ea0-480">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="f6ea0-481">はい</span><span class="sxs-lookup"><span data-stu-id="f6ea0-481">Yes</span></span>                  | <span data-ttu-id="f6ea0-482">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-482">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="f6ea0-483">いいえ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-483">No</span></span>                   | [<span data-ttu-id="f6ea0-484">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="f6ea0-484">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="f6ea0-485">はい</span><span class="sxs-lookup"><span data-stu-id="f6ea0-485">Yes</span></span>                  | <span data-ttu-id="f6ea0-486">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="f6ea0-486">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="f6ea0-487">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-487">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="f6ea0-488">ミドルウェアを使用すると、カスタム`Accept-Encoding`ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-488">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="f6ea0-489">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-489">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="f6ea0-490">ミドルウェアは、圧縮スキームの優先順位を決定するために`q`クライアントから送信されるときに、品質の値 (qvalue,) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-490">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="f6ea0-491">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-491">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="f6ea0-492">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-492">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="f6ea0-493">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-493">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="f6ea0-494">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-494">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="f6ea0-495">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-495">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="f6ea0-496">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-496">Header</span></span>             | <span data-ttu-id="f6ea0-497">Role</span><span class="sxs-lookup"><span data-stu-id="f6ea0-497">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="f6ea0-498">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-498">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="f6ea0-499">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-499">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="f6ea0-500">圧縮が発生すると`Content-Length` 、応答が圧縮されるときに本文の内容が変更されるため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-500">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="f6ea0-501">圧縮が行われると`Content-MD5` 、本文の内容が変更され、ハッシュが有効でなくなったため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-501">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="f6ea0-502">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-502">Specifies the MIME type of the content.</span></span> <span data-ttu-id="f6ea0-503">すべての応答で、 `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-503">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="f6ea0-504">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-504">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="f6ea0-505">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-505">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="f6ea0-506">の`Accept-Encoding`値がであるサーバーからクライアントとプロキシに送信された`Vary`場合、ヘッダーは、要求の`Accept-Encoding`ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-506">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="f6ea0-507">`Vary: Accept-Encoding`ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-507">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="f6ea0-508">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-508">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="f6ea0-509">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-509">The sample illustrates:</span></span>

* <span data-ttu-id="f6ea0-510">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-510">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="f6ea0-511">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-511">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="f6ea0-512">Package</span><span class="sxs-lookup"><span data-stu-id="f6ea0-512">Package</span></span>

<span data-ttu-id="f6ea0-513">ミドルウェアをプロジェクトに含めるには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-513">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="f6ea0-514">構成</span><span class="sxs-lookup"><span data-stu-id="f6ea0-514">Configuration</span></span>

<span data-ttu-id="f6ea0-515">次のコードは、既定の MIME の種類と[Gzip 圧縮プロバイダー](#gzip-compression-provider)の応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-515">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="f6ea0-516">メモ:</span><span class="sxs-lookup"><span data-stu-id="f6ea0-516">Notes:</span></span>

* <span data-ttu-id="f6ea0-517">`app.UseResponseCompression`応答を圧縮するミドルウェアの前にを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-517">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="f6ea0-518">詳細については、「<xref:fundamentals/middleware/index#middleware-order>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-518">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="f6ea0-519">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用して`Accept-Encoding`要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-519">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="f6ea0-520">ヘッダーを`Accept-Encoding`使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-520">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="f6ea0-521">応答`Content-Encoding`に`Vary`ヘッダーとヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-521">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="f6ea0-524">`Accept-Encoding: gzip`ヘッダーを使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-524">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="f6ea0-525">応答`Content-Encoding`に`Vary`は、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-525">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと gzip の値で表示されます。](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="f6ea0-529">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-529">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="f6ea0-530">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-530">Gzip Compression Provider</span></span>

<span data-ttu-id="f6ea0-531">を使用<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-531">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="f6ea0-532">圧縮プロバイダーがに明示的に追加さ<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>れていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-532">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="f6ea0-533">Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-533">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="f6ea0-534">クライアントで Gzip 圧縮がサポートされている場合、圧縮の既定値は Gzip です。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-534">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="f6ea0-535">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-535">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="f6ea0-536">圧縮レベルをに<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>設定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-536">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="f6ea0-537">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-537">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="f6ea0-538">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-538">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="f6ea0-539">Compression Level</span><span class="sxs-lookup"><span data-stu-id="f6ea0-539">Compression Level</span></span> | <span data-ttu-id="f6ea0-540">[説明]</span><span class="sxs-lookup"><span data-stu-id="f6ea0-540">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="f6ea0-541">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-541">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-542">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-542">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="f6ea0-543">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-543">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-544">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-544">No compression should be performed.</span></span> |
| [<span data-ttu-id="f6ea0-545">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="f6ea0-545">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6ea0-546">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-546">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="f6ea0-547">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f6ea0-547">Custom providers</span></span>

<span data-ttu-id="f6ea0-548">を使用して、 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-548">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="f6ea0-549">は<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 、この`ICompressionProvider`によって生成されるコンテンツエンコーディングを表します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-549">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="f6ea0-550">ミドルウェアは、この情報を使用して、要求の`Accept-Encoding`ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-550">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="f6ea0-551">サンプルアプリを使用して、クライアントは`Accept-Encoding: mycustomcompression`ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-551">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f6ea0-552">ミドルウェアはカスタム圧縮実装を使用して、応答を`Content-Encoding: mycustomcompression`ヘッダーと共に返します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-552">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f6ea0-553">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-553">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="f6ea0-554">`Accept-Encoding: mycustomcompression`ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-554">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="f6ea0-555">応答`Vary`に`Content-Encoding`は、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-555">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="f6ea0-556">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-556">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="f6ea0-557">サンプルの`CustomCompressionProvider`クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-557">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="f6ea0-558">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-558">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="f6ea0-561">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="f6ea0-561">MIME types</span></span>

<span data-ttu-id="f6ea0-562">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-562">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="f6ea0-563">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-563">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="f6ea0-564">などのワイルドカードの MIME 型は`text/*`サポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-564">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="f6ea0-565">このサンプルアプリでは、の MIME `image/svg+xml`の種類を追加し、ASP.NET Core バナーイメージ (*横断幕*) を圧縮して提供します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-565">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="f6ea0-566">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="f6ea0-566">Compression with secure protocol</span></span>

<span data-ttu-id="f6ea0-567">セキュリティで保護された接続での圧縮`EnableForHttps`された応答は、既定では無効になっているオプションを使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-567">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="f6ea0-568">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-568">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="f6ea0-569">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="f6ea0-569">Adding the Vary header</span></span>

<span data-ttu-id="f6ea0-570">`Accept-Encoding`ヘッダーに基づいて応答を圧縮する場合、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-570">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="f6ea0-571">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシ`Vary`キャッシュに指示するため`Accept-Encoding`に、ヘッダーに値を追加します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-571">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="f6ea0-572">ASP.NET Core 2.0 以降では、応答が圧縮さ`Vary`れるときに、ミドルウェアによってヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-572">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="f6ea0-573">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="f6ea0-573">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="f6ea0-574">要求が Nginx によってプロキシされ`Accept-Encoding`ている場合、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-574">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="f6ea0-575">`Accept-Encoding`ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-575">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="f6ea0-576">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-576">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="f6ea0-577">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-577">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="f6ea0-578">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="f6ea0-578">Working with IIS dynamic compression</span></span>

<span data-ttu-id="f6ea0-579">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-579">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="f6ea0-580">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-580">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f6ea0-581">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="f6ea0-581">Troubleshooting</span></span>

<span data-ttu-id="f6ea0-582">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用します。これにより、 `Accept-Encoding`要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-582">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="f6ea0-583">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-583">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="f6ea0-584">ヘッダー `Accept-Encoding`には`gzip`、設定したカスタム圧縮`*`プロバイダーに一致する値、、またはカスタムエンコーディングが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-584">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="f6ea0-585">値は、 `identity`または品質値 (qvalue, `q`) の 0 (ゼロ) に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-585">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="f6ea0-586">MIME の種類 (`Content-Type`) を設定し、で構成されている mime の<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-586">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="f6ea0-587">要求にヘッダーを`Content-Range`含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-587">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="f6ea0-588">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6ea0-588">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="f6ea0-589">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="f6ea0-589">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6ea0-590">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f6ea0-590">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="f6ea0-591">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="f6ea0-591">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="f6ea0-592">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="f6ea0-592">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="f6ea0-593">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="f6ea0-593">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="f6ea0-594">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="f6ea0-594">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
