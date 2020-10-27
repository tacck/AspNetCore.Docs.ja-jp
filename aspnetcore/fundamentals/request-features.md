---
title: ASP.NET Core での要求機能
author: ardalis
description: ASP.NET Core のインターフェイスに定義されている HTTP 要求と応答に関連する Web サーバーの実装に関する詳細を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
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
uid: fundamentals/request-features
ms.openlocfilehash: 879b775ba2998ee803708ebf231b5fcd363b811c
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326439"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="7a420-103">ASP.NET Core での要求機能</span><span class="sxs-lookup"><span data-stu-id="7a420-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="7a420-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7a420-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7a420-105">アプリケーションとミドルウェアで要求を処理するために使用する `HttpContext` API には、" *機能インターフェイス* " と呼ばれる抽象化レイヤーがその下にあります。</span><span class="sxs-lookup"><span data-stu-id="7a420-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer undernieth it called *feature interfaces* .</span></span> <span data-ttu-id="7a420-106">各機能インターフェイスには、`HttpContext` によってよって公開される機能の詳細なサブセットが用意されています。</span><span class="sxs-lookup"><span data-stu-id="7a420-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="7a420-107">これらのインターフェイスは、`HttpContext` 全体を再実装しなくても、要求の処理に応じて、サーバーまたはミドルウェアで追加、変更、ラップ、置換、または削除することができます。</span><span class="sxs-lookup"><span data-stu-id="7a420-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="7a420-108">また、テスト時に機能をモックするためにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="7a420-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="7a420-109">機能のコレクション</span><span class="sxs-lookup"><span data-stu-id="7a420-109">Feature collections</span></span>

<span data-ttu-id="7a420-110">`HttpContext` の <xref:Microsoft.AspNetCore.Http.HttpContext.Features> プロパティによって、現在の要求の機能インターフェイスのコレクションへのアクセスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="7a420-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="7a420-111">機能のコレクションは要求のコンテキスト内でも変更可能であるため、コレクションの変更と、その他の機能のサポートの追加にはミドルウェアを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7a420-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="7a420-112">一部の高度な機能は、関連付けられているインターフェイスに機能コレクションを通じてアクセスすることによってのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="7a420-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="7a420-113">機能インターフェイス</span><span class="sxs-lookup"><span data-stu-id="7a420-113">Feature interfaces</span></span>

<span data-ttu-id="7a420-114">ASP.NET Core では、多くの一般的な HTTP 機能インターフェイスが <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> で定義されています。これは、サポートされている機能を識別するために、さまざまなサーバーとミドルウェアによって共有されます。</span><span class="sxs-lookup"><span data-stu-id="7a420-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="7a420-115">サーバーとミドルウェアには、追加の機能を備えた独自のインターフェイスが用意されている場合もあります。</span><span class="sxs-lookup"><span data-stu-id="7a420-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="7a420-116">ほとんどの機能インターフェイスには、省略可能なライトアップ機能が備わっており、その機能が存在しない場合は、それらに関連付けられた `HttpContext` API によって既定値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="7a420-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="7a420-117">いくつかのインターフェイスは必要に応じて次のコンテンツで示されます。これらには要求と応答のコア機能が備わっており、要求を処理するために実装する必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="7a420-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="7a420-118">次の機能インターフェイスは <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> からのものです。</span><span class="sxs-lookup"><span data-stu-id="7a420-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="7a420-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: プロトコル、パス、クエリ文字列、ヘッダー、本文などの HTTP 要求の構造を定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="7a420-120">この機能は、要求を処理するために必要です。</span><span class="sxs-lookup"><span data-stu-id="7a420-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="7a420-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: 状態コード、ヘッダー、応答の本文などの HTTP 応答の構造を定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="7a420-122">この機能は、要求を処理するために必要です。</span><span class="sxs-lookup"><span data-stu-id="7a420-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a420-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: `Stream`、`PipeWriter`、またはファイルのいずれかを使用して、応答本文を書き出すためのさまざまな方法を定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="7a420-124">この機能は、要求を処理するために必要です。</span><span class="sxs-lookup"><span data-stu-id="7a420-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="7a420-125">これにより、`IHttpResponseFeature.Body` と `IHttpSendFileFeature` が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="7a420-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="7a420-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: 現在要求に関連付けられている <xref:System.Security.Claims.ClaimsPrincipal> を保持します。</span><span class="sxs-lookup"><span data-stu-id="7a420-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="7a420-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: 受信 HTTP およびマルチパート フォーム送信を解析およびキャッシュするために使用します。</span><span class="sxs-lookup"><span data-stu-id="7a420-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7a420-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: 要求または応答の本文に対して同期 IO 操作を許可するかどうかを制御するために使用します。</span><span class="sxs-lookup"><span data-stu-id="7a420-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a420-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: 要求や応答のバッファーを無効化する方法を定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="7a420-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: 接続 ID と、ローカルおよびリモートのアドレスとポートのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7a420-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: 現在の要求に対して許可される要求本文の最大サイズを制御します。</span><span class="sxs-lookup"><span data-stu-id="7a420-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7a420-132">`IHttpRequestBodyDetectionFeature`: 要求に本文を含めることができるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="7a420-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="7a420-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: 要求を一意に識別するために実装できるプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="7a420-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="7a420-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: 接続を中止したり、クライアントの切断などによって要求が途中で中止されたかどうかを検出したりするためのサポートを定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a420-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: 存在する場合、要求トレーラー ヘッダーへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="7a420-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="7a420-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: HTTP/2 や HTTP/3 などをサポートするプロトコルのリセット メッセージを送信するために使用します。</span><span class="sxs-lookup"><span data-stu-id="7a420-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7a420-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: サポートされている場合、アプリケーションが応答トレーラー ヘッダーを提供できるようにします。</span><span class="sxs-lookup"><span data-stu-id="7a420-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a420-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: ファイルを非同期的に送信するためのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="7a420-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: [HTTP アップグレード](https://tools.ietf.org/html/rfc2616.html#section-14.42)のサポートを定義します。これにより、サーバーでプロトコルを切り替える必要がある場合に、クライアントで使用したい追加のプロトコルを指定することが可能になります。</span><span class="sxs-lookup"><span data-stu-id="7a420-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="7a420-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Web ソケットをサポートする API を定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a420-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: HTTPS 接続に対して応答圧縮を使用する必要があるかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="7a420-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="7a420-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: 要求ごとのアプリケーション状態の <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> コレクションを格納します。</span><span class="sxs-lookup"><span data-stu-id="7a420-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="7a420-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: クエリ文字列を解析してキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="7a420-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a420-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: 要求本文を <xref:System.IO.Pipelines.PipeReader> として表します。</span><span class="sxs-lookup"><span data-stu-id="7a420-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="7a420-145"><xref:Microsoft.AspNetCore.Http.Features.IRequest:::no-loc(Cookie):::sFeature>: 要求 `:::no-loc(Cookie):::` ヘッダー値を解析してキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="7a420-145"><xref:Microsoft.AspNetCore.Http.Features.IRequest:::no-loc(Cookie):::sFeature>: Parses and caches the request `:::no-loc(Cookie):::` header values.</span></span>

<span data-ttu-id="7a420-146"><xref:Microsoft.AspNetCore.Http.Features.IResponse:::no-loc(Cookie):::sFeature>: `Set-:::no-loc(Cookie):::` ヘッダーに応答 :::no-loc(cookie)::: を適用する方法を制御します。</span><span class="sxs-lookup"><span data-stu-id="7a420-146"><xref:Microsoft.AspNetCore.Http.Features.IResponse:::no-loc(Cookie):::sFeature>: Controls how response :::no-loc(cookie):::s are applied to the `Set-:::no-loc(Cookie):::` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7a420-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: この機能により、IIS によって提供されるような要求サーバー変数にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7a420-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="7a420-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: スコープ付き要求サービスで <xref:System.IServiceProvider> へのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="7a420-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="7a420-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: ユーザー セッションをサポートする `ISessionFactory` と <xref:Microsoft.AspNetCore.Http.ISession> の抽象化を定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="7a420-150">`ISessionFeature` は <xref:Microsoft.AspNetCore.Session.SessionMiddleware> によって実装されます (<xref:fundamentals/app-state> を参照)。</span><span class="sxs-lookup"><span data-stu-id="7a420-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="7a420-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: クライアント証明書を取得する API を定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="7a420-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: TLS トークンのバインド パラメーターを使用するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="7a420-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="7a420-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: サイトのアクティビティと機能に関連するユーザー情報の保存に関するユーザーの同意を照会、許可、および取り消すために使用します。</span><span class="sxs-lookup"><span data-stu-id="7a420-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7a420-154">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="7a420-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
