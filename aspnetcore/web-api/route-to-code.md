---
title: ASP.NET Core での Basic JSON Api Route-to-code
author: jamesnk
description: Route-to-codeおよび json 拡張メソッドを使用して、軽量の json Web api を作成する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: 1f5f532053f8f5ca7f73df8c1a910a484e2488d9
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513097"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>ASP.NET Core での Basic JSON Api Route-to-code

作成者: [James Newton-King](https://github.com/jamesnk)

ASP.NET Core は、JSON web Api を作成するさまざまな方法をサポートしています。

* [ASP.NET Core WEB api](xref:web-api/index) は、api を作成するための完全なフレームワークを提供します。 サービスは、から継承することによって作成され <xref:Microsoft.AspNetCore.Mvc.ControllerBase> ます。 フレームワークによって提供される機能の中には、モデルバインド、検証、コンテンツネゴシエーション、入力と出力の書式設定、OpenAPI などがあります。
* Route-to-code は、ASP.NET Core web API に代わる非フレームワークです。 Route-to-code[ASP.NET Core ルーティング](xref:fundamentals/routing)をコードに直接接続します。 コードは要求から読み取り、応答を書き込みます。 Route-to-code は web API の高度な機能を備えていませんが、それを使用するために必要な構成もありません。

Route-to-code は、小規模で基本的な JSON web Api を構築する場合に適した方法です。

## <a name="create-json-web-apis"></a>JSON web Api の作成

ASP.NET Core には、JSON web Api の作成を容易にするヘルパーメソッドが用意されています。

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> ヘッダーで `Content-Type` JSON コンテンツタイプを確認します。
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> 要求から JSON を読み取り、それを指定された型に逆シリアル化します。
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> 指定された値を JSON として応答本文に書き込み、応答のコンテンツタイプをに設定し `application/json` ます。

軽量のルートベースの JSON Api は、 *Startup.cs* で指定されています。 ルートと API ロジックは、 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> アプリの要求パイプラインの一部としてで構成されます。

### <a name="write-json-response"></a>JSON 応答の書き込み

アプリの JSON API を構成する次のコードについて考えてみます。

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

上記のコードにより、次のことが行われます。

* をルートテンプレートとして使用して、HTTP GET API エンドポイントを追加し `/hello/{name:alpha}` ます。
* ルートが一致すると、API は `name` 要求からルート値を読み取ります。
* 匿名型をと共に JSON 応答として書き込み `WriteAsJsonAsync` ます。

### <a name="read-json-request"></a>JSON 要求の読み取り

`HasJsonContentType` およびは、 `ReadFromJsonAsync` ルートベースの JSON API で json 応答を逆シリアル化するために使用できます。

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

上記のコードにより、次のことが行われます。

* をルートテンプレートとして使用して、HTTP POST API エンドポイントを追加し `/weather` ます。
* ルートが一致すると、は `HasJsonContentType` 要求のコンテンツの種類を検証します。 JSON 以外のコンテンツタイプは、415状態コードを返します。
* コンテンツの種類が JSON の場合、要求コンテンツはによって逆シリアル化され `ReadFromJsonAsync` ます。

### <a name="configure-json-serialization"></a>JSON シリアル化の構成

JSON のシリアル化をカスタマイズするには、次の2つの方法があります。

* 既定のシリアル化オプションは、メソッドでを使用して構成でき `JsonOptions` `Startup.ConfigureServices` ます。
* `WriteAsJsonAsync` および `ReadFromJsonAsync` には、オブジェクトを受け入れるオーバーロードがあり `JsonSerializerOptions` ます。 この `JsonSerializerOptions` オブジェクトは、既定のオプションをオーバーライドします。

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>認証と承認

Route-to-code 認証と承認をサポートします。 やなどの属性は、 `[Authorize]` `[AllowAnonymous]` 要求デリゲートにマップされるエンドポイントには配置できません。 代わりに、および拡張メソッドを使用して認証メタデータが追加され `RequireAuthorization` `AllowAnonymous` ます。

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>依存関係の挿入

コンストラクターを使用した[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)は、では実行できません Route-to-code 。 Web API は、コンストラクターに挿入されたサービスを使用してコントローラーを作成します。 エンドポイントの実行時に型は作成されないため、サービスを手動で解決する必要があります。

ルートベースの Api では、を使用して <xref:System.IServiceProvider> サービスを解決できます。

* などの一時的およびスコープの有効期間サービスは `DbContext` 、エンドポイントの要求デリゲート内の [HttpContext サービス](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) から解決する必要があります。
* などのシングルトン有効期間サービスは `ILogger` 、 [IEndpointRouteBuilder](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)から解決できます。 サービスは、要求デリゲートの外部で解決し、エンドポイント間で共有できます。

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

DI を多用する Api では、DI をサポートする ASP.NET Core アプリの種類を使用することを検討してください。 たとえば、ASP.NET Core web API です。 コントローラーのコンストラクターを使用したサービスの挿入は、サービスを手動で解決するよりも簡単です。

## <a name="api-project-structure"></a>API プロジェクトの構造

ルートベースの Api は、 *Startup.cs* に配置する必要はありません。 Api は他のファイルに配置し、での起動時にマップでき `UseEndpoints` ます。 この方法では、スタートアップ構成ファイルのサイズが小さくなります。

メソッドを定義する次の静的クラスを考えてみ `UserApi` `Map` ます。 メソッドは、ルートベースの Api をマップします。

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

メソッドでは、 `Startup.Configure` `Map` メソッドとその他のクラスの静的メソッドがで呼び出され `UseEndpoints` ます。

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Web API と比較した重要でない機能

Route-to-code は、基本的な JSON Api 向けに設計されています。 ASP.NET Core Web API によって提供される高度な機能の多くはサポートされていません。

によって提供されない機能 Route-to-code は次のとおりです。

* モデル バインド
* モデルの検証
* API を開く/Swagger
* コンテンツ ネゴシエーション
* コンストラクターの依存関係の挿入
* `ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))

前の一覧の一部の機能が必要な場合は、 [ASP.NET Core WEB api](xref:web-api/index) を使用して API を作成することを検討してください。

## <a name="additional-resources"></a>その他のリソース

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
