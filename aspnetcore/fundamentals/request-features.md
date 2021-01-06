---
title: ASP.NET Core での要求機能
author: ardalis
description: ASP.NET Core のインターフェイスに定義されている HTTP 要求と応答に関連する Web サーバーの実装に関する詳細を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "95870426"
---
# <a name="request-features-in-aspnet-core"></a>ASP.NET Core での要求機能

作成者: [Steve Smith](https://ardalis.com/)

アプリケーションとミドルウェアで要求を処理するために使用する `HttpContext` API には、"*機能インターフェイス*" と呼ばれる抽象化レイヤーがその下にあります。 各機能インターフェイスには、`HttpContext` によってよって公開される機能の詳細なサブセットが用意されています。 これらのインターフェイスは、`HttpContext` 全体を再実装しなくても、要求の処理に応じて、サーバーまたはミドルウェアで追加、変更、ラップ、置換、または削除することができます。 また、テスト時に機能をモックするためにも使用できます。

## <a name="feature-collections"></a>機能のコレクション

`HttpContext` の <xref:Microsoft.AspNetCore.Http.HttpContext.Features> プロパティによって、現在の要求の機能インターフェイスのコレクションへのアクセスが提供されます。 機能のコレクションは要求のコンテキスト内でも変更可能であるため、コレクションの変更と、その他の機能のサポートの追加にはミドルウェアを使用できます。 一部の高度な機能は、関連付けられているインターフェイスに機能コレクションを通じてアクセスすることによってのみ使用できます。

## <a name="feature-interfaces"></a>機能インターフェイス

ASP.NET Core では、多くの一般的な HTTP 機能インターフェイスが <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> で定義されています。これは、サポートされている機能を識別するために、さまざまなサーバーとミドルウェアによって共有されます。 サーバーとミドルウェアには、追加の機能を備えた独自のインターフェイスが用意されている場合もあります。

ほとんどの機能インターフェイスには、省略可能なライトアップ機能が備わっており、その機能が存在しない場合は、それらに関連付けられた `HttpContext` API によって既定値が提供されます。 いくつかのインターフェイスは必要に応じて次のコンテンツで示されます。これらには要求と応答のコア機能が備わっており、要求を処理するために実装する必要があるためです。

次の機能インターフェイスは <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> からのものです。

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: プロトコル、パス、クエリ文字列、ヘッダー、本文などの HTTP 要求の構造を定義します。 この機能は、要求を処理するために必要です。

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: 状態コード、ヘッダー、応答の本文などの HTTP 応答の構造を定義します。 この機能は、要求を処理するために必要です。

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: `Stream`、`PipeWriter`、またはファイルのいずれかを使用して、応答本文を書き出すためのさまざまな方法を定義します。 この機能は、要求を処理するために必要です。 これにより、`IHttpResponseFeature.Body` と `IHttpSendFileFeature` が置き換えられます。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: 現在要求に関連付けられている <xref:System.Security.Claims.ClaimsPrincipal> を保持します。

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: 受信 HTTP およびマルチパート フォーム送信を解析およびキャッシュするために使用します。

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: 要求または応答の本文に対して同期 IO 操作を許可するかどうかを制御するために使用します。

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: 要求や応答のバッファーを無効化する方法を定義します。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: 接続 ID と、ローカルおよびリモートのアドレスとポートのプロパティを定義します。

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: 現在の要求に対して許可される要求本文の最大サイズを制御します。

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: 要求に本文を含めることができるかどうかを示します。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: 要求を一意に識別するために実装できるプロパティを追加します。

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: 接続を中止したり、クライアントの切断などによって要求が途中で中止されたかどうかを検出したりするためのサポートを定義します。

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: 存在する場合、要求トレーラー ヘッダーへのアクセスを提供します。

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: HTTP/2 や HTTP/3 などをサポートするプロトコルのリセット メッセージを送信するために使用します。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: サポートされている場合、アプリケーションが応答トレーラー ヘッダーを提供できるようにします。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: ファイルを非同期的に送信するためのメソッドを定義します。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: [HTTP アップグレード](https://tools.ietf.org/html/rfc2616.html#section-14.42)のサポートを定義します。これにより、サーバーでプロトコルを切り替える必要がある場合に、クライアントで使用したい追加のプロトコルを指定することが可能になります。

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Web ソケットをサポートする API を定義します。

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: HTTPS 接続に対して応答圧縮を使用する必要があるかどうかを制御します。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: 要求ごとのアプリケーション状態の <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> コレクションを格納します。

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: クエリ文字列を解析してキャッシュします。
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: 要求本文を <xref:System.IO.Pipelines.PipeReader> として表します。
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: 要求 `Cookie` ヘッダー値を解析してキャッシュします。

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: `Set-Cookie` ヘッダーに応答 cookie を適用する方法を制御します。

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: この機能により、IIS によって提供されるような要求サーバー変数にアクセスできます。

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: スコープ付き要求サービスで <xref:System.IServiceProvider> へのアクセスを提供します。

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: ユーザー セッションをサポートする `ISessionFactory` と <xref:Microsoft.AspNetCore.Http.ISession> の抽象化を定義します。 `ISessionFeature` は <xref:Microsoft.AspNetCore.Session.SessionMiddleware> によって実装されます (<xref:fundamentals/app-state> を参照)。

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: クライアント証明書を取得する API を定義します。

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: TLS トークンのバインド パラメーターを使用するメソッドを定義します。
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: サイトのアクティビティと機能に関連するユーザー情報の保存に関するユーザーの同意を照会、許可、および取り消すために使用します。
   
::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
