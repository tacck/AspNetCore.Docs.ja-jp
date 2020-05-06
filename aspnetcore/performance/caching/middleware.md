---
title: ASP.NET Core での応答キャッシュミドルウェア
author: rick-anderson
description: ASP.NET Core で応答キャッシュ ミドルウェアを構成し、使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/middleware
ms.openlocfilehash: 360b85d642ad3104d2662a38acd8c86e3c56f292
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775051"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="dcb89-103">ASP.NET Core での応答キャッシュミドルウェア</span><span class="sxs-lookup"><span data-stu-id="dcb89-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="dcb89-104">作成者: [John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="dcb89-104">By [John Luo](https://github.com/JunTaoLuo)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dcb89-105">この記事では、ASP.NET Core アプリで応答キャッシュミドルウェアを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-105">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="dcb89-106">ミドルウェアは、応答をキャッシュ可能にするタイミングを決定し、応答を格納して、キャッシュからの応答を提供します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-106">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="dcb89-107">HTTP キャッシュと[`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性の概要については、「[応答キャッシュ](xref:performance/caching/response)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-107">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="dcb89-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="dcb89-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="dcb89-109">構成</span><span class="sxs-lookup"><span data-stu-id="dcb89-109">Configuration</span></span>

<span data-ttu-id="dcb89-110">応答キャッシュミドルウェアは、共有フレームワークを介して ASP.NET Core アプリで暗黙的に使用できます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-110">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="dcb89-111">で`Startup.ConfigureServices`、応答キャッシュミドルウェアをサービスコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="dcb89-112"><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>拡張メソッドと共にミドルウェアを使用するようにアプリを構成します。これにより、の`Startup.Configure`要求処理パイプラインにミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

<span data-ttu-id="dcb89-113">サンプルアプリでは、後続の要求でキャッシュを制御するためのヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="dcb89-114">[キャッシュコントロール](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash;は、最大10秒間のキャッシュ可能な応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="dcb89-115">[変更](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash;後の要求の[エンコーディング](https://tools.ietf.org/html/rfc7231#section-5.3.4)ヘッダーが元の要求のものと一致する場合にのみ、キャッシュされた応答を提供するようにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="dcb89-116">応答キャッシュミドルウェアは、200 (OK) 状態コードを生成するサーバー応答のみをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-116">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="dcb89-117">[エラーページ](xref:fundamentals/error-handling)を含むその他の応答は、ミドルウェアによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-117">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="dcb89-118">認証されたクライアントのコンテンツを含む応答は、ミドルウェアがそれらの応答を格納してサービスを提供しないように、キャッシュ不可能としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-118">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="dcb89-119">応答がキャッシュ可能かどうかをミドルウェアが決定する方法の詳細については、「[キャッシュの条件](#conditions-for-caching)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-119">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="dcb89-120">Options</span><span class="sxs-lookup"><span data-stu-id="dcb89-120">Options</span></span>

<span data-ttu-id="dcb89-121">応答キャッシュのオプションを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-121">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="dcb89-122">オプション</span><span class="sxs-lookup"><span data-stu-id="dcb89-122">Option</span></span> | <span data-ttu-id="dcb89-123">説明</span><span class="sxs-lookup"><span data-stu-id="dcb89-123">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="dcb89-124">応答本文の最大キャッシュ可能サイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-124">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="dcb89-125">既定値は`64 * 1024 * 1024` (64 MB) です。</span><span class="sxs-lookup"><span data-stu-id="dcb89-125">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="dcb89-126">応答キャッシュミドルウェアのサイズ制限 (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-126">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="dcb89-127">既定値は`100 * 1024 * 1024` (100 MB) です。</span><span class="sxs-lookup"><span data-stu-id="dcb89-127">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="dcb89-128">大文字と小文字が区別されるパスに応答をキャッシュするかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="dcb89-129">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="dcb89-129">The default value is `false`.</span></span> |

<span data-ttu-id="dcb89-130">次の例では、ミドルウェアをに構成します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-130">The following example configures the middleware to:</span></span>

* <span data-ttu-id="dcb89-131">本文のサイズが1024バイト以下の応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-131">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="dcb89-132">大文字と小文字を区別するパスで応答を格納します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-132">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="dcb89-133">たとえば、 `/page1`と`/Page1`は別々に格納されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-133">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="dcb89-134">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="dcb89-134">VaryByQueryKeys</span></span>

<span data-ttu-id="dcb89-135">MVC/web API コントローラーまたは Razor Pages ページモデルを使用する[`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)場合、属性は、応答キャッシュ用の適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-135">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="dcb89-136">ミドルウェアを厳密に必要`[ResponseCache]`とする属性の唯一のパラメーター <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>は、実際の HTTP ヘッダーに対応していません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-136">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="dcb89-137">詳細については、「<xref:performance/caching/response#responsecache-attribute>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-137">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="dcb89-138">`[ResponseCache]`属性を使用しない場合は、を`VaryByQueryKeys`使用して応答のキャッシュを変化させることができます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-138">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="dcb89-139">を HttpContext <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>から直接使用し[ます。](xref:Microsoft.AspNetCore.Http.HttpContext.Features)</span><span class="sxs-lookup"><span data-stu-id="dcb89-139">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="dcb89-140">`*`で`VaryByQueryKeys` 1 つの値を使用すると、すべての要求クエリパラメーターによってキャッシュが異なります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-140">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="dcb89-141">応答キャッシュミドルウェアによって使用される HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="dcb89-141">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="dcb89-142">次の表は、応答のキャッシュに影響を与える HTTP ヘッダーに関する情報を示しています。</span><span class="sxs-lookup"><span data-stu-id="dcb89-142">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="dcb89-143">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="dcb89-143">Header</span></span> | <span data-ttu-id="dcb89-144">詳細</span><span class="sxs-lookup"><span data-stu-id="dcb89-144">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="dcb89-145">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-145">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="dcb89-146">ミドルウェアは、 `public` cache ディレクティブでマークされたキャッシュ応答のみを考慮します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-146">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="dcb89-147">次のパラメーターを使用してキャッシュを制御します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-147">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="dcb89-148">最長有効期間</span><span class="sxs-lookup"><span data-stu-id="dcb89-148">max-age</span></span></li><li><span data-ttu-id="dcb89-149">最大-古い&#8224;</span><span class="sxs-lookup"><span data-stu-id="dcb89-149">max-stale&#8224;</span></span></li><li><span data-ttu-id="dcb89-150">最小-新規</span><span class="sxs-lookup"><span data-stu-id="dcb89-150">min-fresh</span></span></li><li><span data-ttu-id="dcb89-151">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="dcb89-151">must-revalidate</span></span></li><li><span data-ttu-id="dcb89-152">no-cache</span><span class="sxs-lookup"><span data-stu-id="dcb89-152">no-cache</span></span></li><li><span data-ttu-id="dcb89-153">ストアなし</span><span class="sxs-lookup"><span data-stu-id="dcb89-153">no-store</span></span></li><li><span data-ttu-id="dcb89-154">-if-キャッシュ済み</span><span class="sxs-lookup"><span data-stu-id="dcb89-154">only-if-cached</span></span></li><li><span data-ttu-id="dcb89-155">プライベート</span><span class="sxs-lookup"><span data-stu-id="dcb89-155">private</span></span></li><li><span data-ttu-id="dcb89-156">public</span><span class="sxs-lookup"><span data-stu-id="dcb89-156">public</span></span></li><li><span data-ttu-id="dcb89-157">s-maxage</span><span class="sxs-lookup"><span data-stu-id="dcb89-157">s-maxage</span></span></li><li><span data-ttu-id="dcb89-158">プロキシ再検証&#8225;</span><span class="sxs-lookup"><span data-stu-id="dcb89-158">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="dcb89-159">&#8224;に`max-stale`制限が指定されていない場合、ミドルウェアは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-159">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="dcb89-160">&#8225;`proxy-revalidate`はと`must-revalidate`同じ効果があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-160">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="dcb89-161">詳細については、「 [RFC 7231: Request Cache-control ディレクティブ](https://tools.ietf.org/html/rfc7234#section-5.2.1)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-161">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="dcb89-162">要求`Pragma: no-cache`のヘッダーでは、と`Cache-Control: no-cache`同じ効果が得られます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-162">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="dcb89-163">このヘッダーは、 `Cache-Control`ヘッダー内の関連するディレクティブによってオーバーライドされます (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-163">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="dcb89-164">HTTP/1.0 との下位互換性のために考慮されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-164">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="dcb89-165">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-165">The response isn't cached if the header exists.</span></span> <span data-ttu-id="dcb89-166">1つ以上の cookie を設定する要求処理パイプライン内のミドルウェアは、応答キャッシュミドルウェアが応答をキャッシュしないようにします ( [cookie ベースの TempData プロバイダー](xref:fundamentals/app-state#tempdata)など)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-166">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="dcb89-167">ヘッダー `Vary`は、キャッシュされた応答を別のヘッダーによって変更するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-167">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="dcb89-168">たとえば、 `Vary: Accept-Encoding`ヘッダーを含めることによって、エンコードによって応答をキャッシュします`Accept-Encoding: gzip` 。 `Accept-Encoding: text/plain`ヘッダーとは別に要求の応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-168">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="dcb89-169">ヘッダー値がの`*`応答は格納されません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-169">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="dcb89-170">このヘッダーによって古いと見なされる応答は、他の`Cache-Control`ヘッダーでオーバーライドされない限り、格納または取得されません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-170">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="dcb89-171">値が`*`ではなく、応答のが`ETag`指定された値と一致しない場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-171">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="dcb89-172">それ以外の場合は、304 (変更なし) の応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-172">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="dcb89-173">`If-None-Match`ヘッダーが存在しない場合、キャッシュされた応答の日付が指定した値より新しい場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-173">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="dcb89-174">それ以外の場合は、 *304-変更されていない*応答が提供されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-174">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="dcb89-175">キャッシュからサービスを提供し`Date`ている場合、ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-175">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="dcb89-176">キャッシュからサービスを提供し`Content-Length`ている場合、ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-176">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="dcb89-177">元`Age`の応答で送信されたヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-177">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="dcb89-178">ミドルウェアは、キャッシュされた応答を提供するときに新しい値を計算します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-178">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="dcb89-179">キャッシュは、要求のキャッシュ制御ディレクティブに従います。</span><span class="sxs-lookup"><span data-stu-id="dcb89-179">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="dcb89-180">ミドルウェアは、 [HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234#section-5.2)の規則を尊重します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-180">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="dcb89-181">規則では、クライアントによって送信`Cache-Control`された有効なヘッダーを受け入れるためにキャッシュが必要です。</span><span class="sxs-lookup"><span data-stu-id="dcb89-181">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="dcb89-182">この仕様では、クライアントは`no-cache`ヘッダー値を使用して要求を行い、すべての要求に対してサーバーに新しい応答を強制的に生成させることができます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-182">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="dcb89-183">現時点では、ミドルウェアが公式のキャッシュ仕様に準拠しているため、ミドルウェアを使用する場合、このキャッシュ動作を開発者が制御することはありません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-183">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="dcb89-184">キャッシュの動作を詳細に制御するには、ASP.NET Core の他のキャッシュ機能を調べます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-184">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="dcb89-185">次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-185">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="dcb89-186">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="dcb89-186">Troubleshooting</span></span>

<span data-ttu-id="dcb89-187">キャッシュの動作が想定どおりでない場合は、応答がキャッシュ可能であり、キャッシュから提供できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-187">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="dcb89-188">要求の受信ヘッダーと応答の送信ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-188">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="dcb89-189">デバッグに役立つように[ログ記録](xref:fundamentals/logging/index)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-189">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="dcb89-190">キャッシュ動作のテストとトラブルシューティングを行う場合、ブラウザーでは、望ましくない方法でキャッシュに影響を与える要求ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-190">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="dcb89-191">たとえば、ページを更新するときに`Cache-Control` 、ブラウザー `no-cache`で`max-age=0`ヘッダーをまたはに設定できます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-191">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="dcb89-192">次のツールでは、要求ヘッダーを明示的に設定でき、キャッシュのテストに適しています。</span><span class="sxs-lookup"><span data-stu-id="dcb89-192">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="dcb89-193">Fiddler</span><span class="sxs-lookup"><span data-stu-id="dcb89-193">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="dcb89-194">Postman</span><span class="sxs-lookup"><span data-stu-id="dcb89-194">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="dcb89-195">キャッシュの条件</span><span class="sxs-lookup"><span data-stu-id="dcb89-195">Conditions for caching</span></span>

* <span data-ttu-id="dcb89-196">要求は、200 (OK) 状態コードでサーバー応答を生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-196">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="dcb89-197">要求メソッドは GET または HEAD である必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-197">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="dcb89-198">で`Startup.Configure`は、応答キャッシュミドルウェアは、キャッシュを必要とするミドルウェアの前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-198">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="dcb89-199">詳細については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-199">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="dcb89-200">ヘッダー `Authorization`を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-200">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="dcb89-201">`Cache-Control`ヘッダーパラメーターは有効である必要があり、応答は`public`とマークさ`private`れている必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-201">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="dcb89-202">`Pragma: no-cache`ヘッダーが存在する場合、ヘッダーは`Cache-Control`ヘッダーをオーバーライド`Pragma`するので`Cache-Control` 、ヘッダーが存在しない場合はヘッダーが存在しない必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-202">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="dcb89-203">ヘッダー `Set-Cookie`を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-203">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="dcb89-204">`Vary`ヘッダーパラメーターはと同じである必要が`*`あります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-204">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="dcb89-205">ヘッダー `Content-Length`値 (設定されている場合) は、応答本文のサイズと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-205">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="dcb89-206">は<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>使用されません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-206">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="dcb89-207">`Expires`ヘッダー、および、および`max-age` `s-maxage`の各キャッシュディレクティブで指定されているとおり、応答を古いものにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-207">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="dcb89-208">応答バッファリングを成功させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-208">Response buffering must be successful.</span></span> <span data-ttu-id="dcb89-209">応答のサイズは、構成済みまたは既定値<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>よりも小さくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-209">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="dcb89-210">応答の本文のサイズは、構成済みまたは既定値<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>よりも小さくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-210">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="dcb89-211">応答は、 [RFC 7234](https://tools.ietf.org/html/rfc7234)仕様に従ってキャッシュ可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-211">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="dcb89-212">たとえば、 `no-store`ディレクティブは、要求または応答のヘッダーフィールドに存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-212">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="dcb89-213">詳細については、 *「セクション 3:* [RFC 7234](https://tools.ietf.org/html/rfc7234)のキャッシュに応答を格納する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-213">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="dcb89-214">クロスサイト要求偽造 (CSRF) 攻撃を防ぐために、セキュリティで保護されたトークンを`Cache-Control`生成`Pragma`するため`no-cache`の偽造防止システムは、応答がキャッシュされないようにヘッダーとヘッダーをに設定します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-214">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="dcb89-215">HTML フォーム要素の偽造防止トークンを無効にする方法について<xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-215">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dcb89-216">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="dcb89-216">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dcb89-217">この記事では、ASP.NET Core アプリで応答キャッシュミドルウェアを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-217">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="dcb89-218">ミドルウェアは、応答をキャッシュ可能にするタイミングを決定し、応答を格納して、キャッシュからの応答を提供します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-218">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="dcb89-219">HTTP キャッシュと[`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性の概要については、「[応答キャッシュ](xref:performance/caching/response)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-219">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="dcb89-220">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="dcb89-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="dcb89-221">構成</span><span class="sxs-lookup"><span data-stu-id="dcb89-221">Configuration</span></span>

<span data-ttu-id="dcb89-222">[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を使用するか、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-222">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="dcb89-223">で`Startup.ConfigureServices`、応答キャッシュミドルウェアをサービスコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-223">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="dcb89-224"><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>拡張メソッドと共にミドルウェアを使用するようにアプリを構成します。これにより、の`Startup.Configure`要求処理パイプラインにミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-224">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="dcb89-225">サンプルアプリでは、後続の要求でキャッシュを制御するためのヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-225">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="dcb89-226">[キャッシュコントロール](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash;は、最大10秒間のキャッシュ可能な応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-226">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="dcb89-227">[変更](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash;後の要求の[エンコーディング](https://tools.ietf.org/html/rfc7231#section-5.3.4)ヘッダーが元の要求のものと一致する場合にのみ、キャッシュされた応答を提供するようにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-227">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="dcb89-228">応答キャッシュミドルウェアは、200 (OK) 状態コードを生成するサーバー応答のみをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-228">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="dcb89-229">[エラーページ](xref:fundamentals/error-handling)を含むその他の応答は、ミドルウェアによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-229">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="dcb89-230">認証されたクライアントのコンテンツを含む応答は、ミドルウェアがそれらの応答を格納してサービスを提供しないように、キャッシュ不可能としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-230">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="dcb89-231">応答がキャッシュ可能かどうかをミドルウェアが決定する方法の詳細については、「[キャッシュの条件](#conditions-for-caching)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-231">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="dcb89-232">Options</span><span class="sxs-lookup"><span data-stu-id="dcb89-232">Options</span></span>

<span data-ttu-id="dcb89-233">応答キャッシュのオプションを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-233">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="dcb89-234">オプション</span><span class="sxs-lookup"><span data-stu-id="dcb89-234">Option</span></span> | <span data-ttu-id="dcb89-235">説明</span><span class="sxs-lookup"><span data-stu-id="dcb89-235">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="dcb89-236">応答本文の最大キャッシュ可能サイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-236">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="dcb89-237">既定値は`64 * 1024 * 1024` (64 MB) です。</span><span class="sxs-lookup"><span data-stu-id="dcb89-237">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="dcb89-238">応答キャッシュミドルウェアのサイズ制限 (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-238">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="dcb89-239">既定値は`100 * 1024 * 1024` (100 MB) です。</span><span class="sxs-lookup"><span data-stu-id="dcb89-239">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="dcb89-240">大文字と小文字が区別されるパスに応答をキャッシュするかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-240">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="dcb89-241">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="dcb89-241">The default value is `false`.</span></span> |

<span data-ttu-id="dcb89-242">次の例では、ミドルウェアをに構成します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-242">The following example configures the middleware to:</span></span>

* <span data-ttu-id="dcb89-243">本文のサイズが1024バイト以下の応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-243">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="dcb89-244">大文字と小文字を区別するパスで応答を格納します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-244">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="dcb89-245">たとえば、 `/page1`と`/Page1`は別々に格納されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-245">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="dcb89-246">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="dcb89-246">VaryByQueryKeys</span></span>

<span data-ttu-id="dcb89-247">MVC/web API コントローラーまたは Razor Pages ページモデルを使用する[`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)場合、属性は、応答キャッシュ用の適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-247">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="dcb89-248">ミドルウェアを厳密に必要`[ResponseCache]`とする属性の唯一のパラメーター <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>は、実際の HTTP ヘッダーに対応していません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-248">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="dcb89-249">詳細については、「<xref:performance/caching/response#responsecache-attribute>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-249">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="dcb89-250">`[ResponseCache]`属性を使用しない場合は、を`VaryByQueryKeys`使用して応答のキャッシュを変化させることができます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-250">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="dcb89-251">を HttpContext <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>から直接使用し[ます。](xref:Microsoft.AspNetCore.Http.HttpContext.Features)</span><span class="sxs-lookup"><span data-stu-id="dcb89-251">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="dcb89-252">`*`で`VaryByQueryKeys` 1 つの値を使用すると、すべての要求クエリパラメーターによってキャッシュが異なります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-252">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="dcb89-253">応答キャッシュミドルウェアによって使用される HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="dcb89-253">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="dcb89-254">次の表は、応答のキャッシュに影響を与える HTTP ヘッダーに関する情報を示しています。</span><span class="sxs-lookup"><span data-stu-id="dcb89-254">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="dcb89-255">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="dcb89-255">Header</span></span> | <span data-ttu-id="dcb89-256">詳細</span><span class="sxs-lookup"><span data-stu-id="dcb89-256">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="dcb89-257">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-257">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="dcb89-258">ミドルウェアは、 `public` cache ディレクティブでマークされたキャッシュ応答のみを考慮します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-258">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="dcb89-259">次のパラメーターを使用してキャッシュを制御します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-259">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="dcb89-260">最長有効期間</span><span class="sxs-lookup"><span data-stu-id="dcb89-260">max-age</span></span></li><li><span data-ttu-id="dcb89-261">最大-古い&#8224;</span><span class="sxs-lookup"><span data-stu-id="dcb89-261">max-stale&#8224;</span></span></li><li><span data-ttu-id="dcb89-262">最小-新規</span><span class="sxs-lookup"><span data-stu-id="dcb89-262">min-fresh</span></span></li><li><span data-ttu-id="dcb89-263">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="dcb89-263">must-revalidate</span></span></li><li><span data-ttu-id="dcb89-264">no-cache</span><span class="sxs-lookup"><span data-stu-id="dcb89-264">no-cache</span></span></li><li><span data-ttu-id="dcb89-265">ストアなし</span><span class="sxs-lookup"><span data-stu-id="dcb89-265">no-store</span></span></li><li><span data-ttu-id="dcb89-266">-if-キャッシュ済み</span><span class="sxs-lookup"><span data-stu-id="dcb89-266">only-if-cached</span></span></li><li><span data-ttu-id="dcb89-267">プライベート</span><span class="sxs-lookup"><span data-stu-id="dcb89-267">private</span></span></li><li><span data-ttu-id="dcb89-268">public</span><span class="sxs-lookup"><span data-stu-id="dcb89-268">public</span></span></li><li><span data-ttu-id="dcb89-269">s-maxage</span><span class="sxs-lookup"><span data-stu-id="dcb89-269">s-maxage</span></span></li><li><span data-ttu-id="dcb89-270">プロキシ再検証&#8225;</span><span class="sxs-lookup"><span data-stu-id="dcb89-270">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="dcb89-271">&#8224;に`max-stale`制限が指定されていない場合、ミドルウェアは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-271">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="dcb89-272">&#8225;`proxy-revalidate`はと`must-revalidate`同じ効果があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-272">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="dcb89-273">詳細については、「 [RFC 7231: Request Cache-control ディレクティブ](https://tools.ietf.org/html/rfc7234#section-5.2.1)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-273">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="dcb89-274">要求`Pragma: no-cache`のヘッダーでは、と`Cache-Control: no-cache`同じ効果が得られます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-274">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="dcb89-275">このヘッダーは、 `Cache-Control`ヘッダー内の関連するディレクティブによってオーバーライドされます (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-275">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="dcb89-276">HTTP/1.0 との下位互換性のために考慮されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-276">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="dcb89-277">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-277">The response isn't cached if the header exists.</span></span> <span data-ttu-id="dcb89-278">1つ以上の cookie を設定する要求処理パイプライン内のミドルウェアは、応答キャッシュミドルウェアが応答をキャッシュしないようにします ( [cookie ベースの TempData プロバイダー](xref:fundamentals/app-state#tempdata)など)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-278">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="dcb89-279">ヘッダー `Vary`は、キャッシュされた応答を別のヘッダーによって変更するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-279">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="dcb89-280">たとえば、 `Vary: Accept-Encoding`ヘッダーを含めることによって、エンコードによって応答をキャッシュします`Accept-Encoding: gzip` 。 `Accept-Encoding: text/plain`ヘッダーとは別に要求の応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-280">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="dcb89-281">ヘッダー値がの`*`応答は格納されません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-281">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="dcb89-282">このヘッダーによって古いと見なされる応答は、他の`Cache-Control`ヘッダーでオーバーライドされない限り、格納または取得されません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-282">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="dcb89-283">値が`*`ではなく、応答のが`ETag`指定された値と一致しない場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-283">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="dcb89-284">それ以外の場合は、304 (変更なし) の応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-284">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="dcb89-285">`If-None-Match`ヘッダーが存在しない場合、キャッシュされた応答の日付が指定した値より新しい場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-285">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="dcb89-286">それ以外の場合は、 *304-変更されていない*応答が提供されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-286">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="dcb89-287">キャッシュからサービスを提供し`Date`ている場合、ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-287">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="dcb89-288">キャッシュからサービスを提供し`Content-Length`ている場合、ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="dcb89-288">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="dcb89-289">元`Age`の応答で送信されたヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-289">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="dcb89-290">ミドルウェアは、キャッシュされた応答を提供するときに新しい値を計算します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-290">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="dcb89-291">キャッシュは、要求のキャッシュ制御ディレクティブに従います。</span><span class="sxs-lookup"><span data-stu-id="dcb89-291">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="dcb89-292">ミドルウェアは、 [HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234#section-5.2)の規則を尊重します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-292">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="dcb89-293">規則では、クライアントによって送信`Cache-Control`された有効なヘッダーを受け入れるためにキャッシュが必要です。</span><span class="sxs-lookup"><span data-stu-id="dcb89-293">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="dcb89-294">この仕様では、クライアントは`no-cache`ヘッダー値を使用して要求を行い、すべての要求に対してサーバーに新しい応答を強制的に生成させることができます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-294">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="dcb89-295">現時点では、ミドルウェアが公式のキャッシュ仕様に準拠しているため、ミドルウェアを使用する場合、このキャッシュ動作を開発者が制御することはありません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-295">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="dcb89-296">キャッシュの動作を詳細に制御するには、ASP.NET Core の他のキャッシュ機能を調べます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-296">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="dcb89-297">次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-297">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="dcb89-298">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="dcb89-298">Troubleshooting</span></span>

<span data-ttu-id="dcb89-299">キャッシュの動作が想定どおりでない場合は、応答がキャッシュ可能であり、キャッシュから提供できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-299">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="dcb89-300">要求の受信ヘッダーと応答の送信ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-300">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="dcb89-301">デバッグに役立つように[ログ記録](xref:fundamentals/logging/index)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="dcb89-301">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="dcb89-302">キャッシュ動作のテストとトラブルシューティングを行う場合、ブラウザーでは、望ましくない方法でキャッシュに影響を与える要求ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-302">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="dcb89-303">たとえば、ページを更新するときに`Cache-Control` 、ブラウザー `no-cache`で`max-age=0`ヘッダーをまたはに設定できます。</span><span class="sxs-lookup"><span data-stu-id="dcb89-303">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="dcb89-304">次のツールでは、要求ヘッダーを明示的に設定でき、キャッシュのテストに適しています。</span><span class="sxs-lookup"><span data-stu-id="dcb89-304">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="dcb89-305">Fiddler</span><span class="sxs-lookup"><span data-stu-id="dcb89-305">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="dcb89-306">Postman</span><span class="sxs-lookup"><span data-stu-id="dcb89-306">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="dcb89-307">キャッシュの条件</span><span class="sxs-lookup"><span data-stu-id="dcb89-307">Conditions for caching</span></span>

* <span data-ttu-id="dcb89-308">要求は、200 (OK) 状態コードでサーバー応答を生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-308">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="dcb89-309">要求メソッドは GET または HEAD である必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-309">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="dcb89-310">で`Startup.Configure`は、応答キャッシュミドルウェアは、キャッシュを必要とするミドルウェアの前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-310">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="dcb89-311">詳細については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-311">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="dcb89-312">ヘッダー `Authorization`を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-312">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="dcb89-313">`Cache-Control`ヘッダーパラメーターは有効である必要があり、応答は`public`とマークさ`private`れている必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-313">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="dcb89-314">`Pragma: no-cache`ヘッダーが存在する場合、ヘッダーは`Cache-Control`ヘッダーをオーバーライド`Pragma`するので`Cache-Control` 、ヘッダーが存在しない場合はヘッダーが存在しない必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-314">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="dcb89-315">ヘッダー `Set-Cookie`を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-315">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="dcb89-316">`Vary`ヘッダーパラメーターはと同じである必要が`*`あります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-316">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="dcb89-317">ヘッダー `Content-Length`値 (設定されている場合) は、応答本文のサイズと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-317">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="dcb89-318">は<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>使用されません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-318">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="dcb89-319">`Expires`ヘッダー、および、および`max-age` `s-maxage`の各キャッシュディレクティブで指定されているとおり、応答を古いものにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-319">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="dcb89-320">応答バッファリングを成功させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-320">Response buffering must be successful.</span></span> <span data-ttu-id="dcb89-321">応答のサイズは、構成済みまたは既定値<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>よりも小さくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-321">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="dcb89-322">応答の本文のサイズは、構成済みまたは既定値<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>よりも小さくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-322">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="dcb89-323">応答は、 [RFC 7234](https://tools.ietf.org/html/rfc7234)仕様に従ってキャッシュ可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcb89-323">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="dcb89-324">たとえば、 `no-store`ディレクティブは、要求または応答のヘッダーフィールドに存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb89-324">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="dcb89-325">詳細については、 *「セクション 3:* [RFC 7234](https://tools.ietf.org/html/rfc7234)のキャッシュに応答を格納する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-325">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="dcb89-326">クロスサイト要求偽造 (CSRF) 攻撃を防ぐために、セキュリティで保護されたトークンを`Cache-Control`生成`Pragma`するため`no-cache`の偽造防止システムは、応答がキャッシュされないようにヘッダーとヘッダーをに設定します。</span><span class="sxs-lookup"><span data-stu-id="dcb89-326">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="dcb89-327">HTML フォーム要素の偽造防止トークンを無効にする方法について<xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb89-327">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dcb89-328">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dcb89-328">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
