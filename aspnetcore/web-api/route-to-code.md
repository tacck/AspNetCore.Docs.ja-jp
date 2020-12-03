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
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556607"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="7e979-103">ASP.NET Core での Basic JSON Api Route-to-code</span><span class="sxs-lookup"><span data-stu-id="7e979-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="7e979-104">作成者: [James Newton-King](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7e979-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="7e979-105">ASP.NET Core は、JSON web Api を作成するさまざまな方法をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="7e979-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="7e979-106">[ASP.NET Core WEB api](xref:web-api/index) は、api を作成するための完全なフレームワークを提供します。</span><span class="sxs-lookup"><span data-stu-id="7e979-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="7e979-107">サービスは、から継承することによって作成され <xref:Microsoft.AspNetCore.Mvc.ControllerBase> ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="7e979-108">フレームワークによって提供される機能の中には、モデルバインド、検証、コンテンツネゴシエーション、入力と出力の書式設定、OpenAPI などがあります。</span><span class="sxs-lookup"><span data-stu-id="7e979-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="7e979-109">Route-to-code は、ASP.NET Core web API に代わる非フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="7e979-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="7e979-110">Route-to-code[ASP.NET Core ルーティング](xref:fundamentals/routing)をコードに直接接続します。</span><span class="sxs-lookup"><span data-stu-id="7e979-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="7e979-111">コードは要求から読み取り、応答を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="7e979-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="7e979-112">Route-to-code は web API の高度な機能を備えていませんが、それを使用するために必要な構成もありません。</span><span class="sxs-lookup"><span data-stu-id="7e979-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="7e979-113">Route-to-code は、小規模で基本的な JSON web Api を構築する場合に適した方法です。</span><span class="sxs-lookup"><span data-stu-id="7e979-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="7e979-114">JSON web Api の作成</span><span class="sxs-lookup"><span data-stu-id="7e979-114">Create JSON web APIs</span></span>

<span data-ttu-id="7e979-115">ASP.NET Core には、JSON web Api の作成を容易にするヘルパーメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="7e979-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="7e979-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> ヘッダーで `Content-Type` JSON コンテンツタイプを確認します。</span><span class="sxs-lookup"><span data-stu-id="7e979-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="7e979-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> 要求から JSON を読み取り、それを指定された型に逆シリアル化します。</span><span class="sxs-lookup"><span data-stu-id="7e979-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="7e979-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> 指定された値を JSON として応答本文に書き込み、応答のコンテンツタイプをに設定し `application/json` ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="7e979-119">軽量のルートベースの JSON Api は、 *Startup.cs* で指定されています。</span><span class="sxs-lookup"><span data-stu-id="7e979-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="7e979-120">ルートと API ロジックは、 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> アプリの要求パイプラインの一部としてで構成されます。</span><span class="sxs-lookup"><span data-stu-id="7e979-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="7e979-121">JSON 応答の書き込み</span><span class="sxs-lookup"><span data-stu-id="7e979-121">Write JSON response</span></span>

<span data-ttu-id="7e979-122">アプリの JSON API を構成する次のコードについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="7e979-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="7e979-123">上記のコードでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="7e979-123">The preceding code:</span></span>

* <span data-ttu-id="7e979-124">をルートテンプレートとして使用して、HTTP GET API エンドポイントを追加し `/hello/{name:alpha}` ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="7e979-125">ルートが一致すると、API は `name` 要求からルート値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="7e979-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="7e979-126">匿名型をと共に JSON 応答として書き込み `WriteAsJsonAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="7e979-127">JSON 要求の読み取り</span><span class="sxs-lookup"><span data-stu-id="7e979-127">Read JSON request</span></span>

<span data-ttu-id="7e979-128">`HasJsonContentType` およびは、 `ReadFromJsonAsync` ルートベースの JSON API で json 応答を逆シリアル化するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="7e979-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="7e979-129">上記のコードでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="7e979-129">The preceding code:</span></span>

* <span data-ttu-id="7e979-130">をルートテンプレートとして使用して、HTTP POST API エンドポイントを追加し `/weather` ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="7e979-131">ルートが一致すると、は `HasJsonContentType` 要求のコンテンツの種類を検証します。</span><span class="sxs-lookup"><span data-stu-id="7e979-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="7e979-132">JSON 以外のコンテンツタイプは、415状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="7e979-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="7e979-133">コンテンツの種類が JSON の場合、要求コンテンツはによって逆シリアル化され `ReadFromJsonAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="7e979-134">JSON シリアル化の構成</span><span class="sxs-lookup"><span data-stu-id="7e979-134">Configure JSON serialization</span></span>

<span data-ttu-id="7e979-135">JSON のシリアル化をカスタマイズするには、次の2つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="7e979-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="7e979-136">既定のシリアル化オプションは、メソッドでを使用して構成でき <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-136">Default serialization options can be configured with <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="7e979-137">`WriteAsJsonAsync` および `ReadFromJsonAsync` には、オブジェクトを受け入れるオーバーロードがあり <xref:System.Text.Json.JsonSerializerOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a <xref:System.Text.Json.JsonSerializerOptions> object.</span></span> <span data-ttu-id="7e979-138">このオプションオブジェクトは、既定のオプションをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="7e979-138">This options object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="7e979-139">認証と承認</span><span class="sxs-lookup"><span data-stu-id="7e979-139">Authentication and authorization</span></span>

<span data-ttu-id="7e979-140">Route-to-code 認証と承認をサポートします。</span><span class="sxs-lookup"><span data-stu-id="7e979-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="7e979-141">やなどの属性は、 `[Authorize]` `[AllowAnonymous]` 要求デリゲートにマップされるエンドポイントには配置できません。</span><span class="sxs-lookup"><span data-stu-id="7e979-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="7e979-142">代わりに、および拡張メソッドを使用して認証メタデータが追加され <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-142">Instead, authorization metadata is added using the <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="7e979-143">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="7e979-143">Dependency injection</span></span>

<span data-ttu-id="7e979-144">コンストラクターを使用した[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)は、では実行できません Route-to-code 。</span><span class="sxs-lookup"><span data-stu-id="7e979-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="7e979-145">Web API は、コンストラクターに挿入されたサービスを使用してコントローラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="7e979-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="7e979-146">エンドポイントの実行時に型は作成されないため、サービスを手動で解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e979-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="7e979-147">ルートベースの Api では、を使用して <xref:System.IServiceProvider> サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="7e979-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="7e979-148">などの一時的およびスコープの有効期間サービスは `DbContext` 、エンドポイントの要求デリゲート内の [HttpContext サービス](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) から解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e979-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="7e979-149">などのシングルトン有効期間サービスは `ILogger` 、 [IEndpointRouteBuilder](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)から解決できます。</span><span class="sxs-lookup"><span data-stu-id="7e979-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="7e979-150">サービスは、要求デリゲートの外部で解決し、エンドポイント間で共有できます。</span><span class="sxs-lookup"><span data-stu-id="7e979-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="7e979-151">DI を多用する Api では、DI をサポートする ASP.NET Core アプリの種類を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="7e979-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="7e979-152">たとえば、ASP.NET Core web API です。</span><span class="sxs-lookup"><span data-stu-id="7e979-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="7e979-153">コントローラーのコンストラクターを使用したサービスの挿入は、サービスを手動で解決するよりも簡単です。</span><span class="sxs-lookup"><span data-stu-id="7e979-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="7e979-154">API プロジェクトの構造</span><span class="sxs-lookup"><span data-stu-id="7e979-154">API project structure</span></span>

<span data-ttu-id="7e979-155">ルートベースの Api は、 *Startup.cs* に配置する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7e979-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="7e979-156">Api は他のファイルに配置し、での起動時にマップでき `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="7e979-157">この方法では、スタートアップ構成ファイルのサイズが小さくなります。</span><span class="sxs-lookup"><span data-stu-id="7e979-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="7e979-158">メソッドを定義する次の静的クラスを考えてみ `UserApi` `Map` ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="7e979-159">メソッドは、ルートベースの Api をマップします。</span><span class="sxs-lookup"><span data-stu-id="7e979-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="7e979-160">メソッドでは、 `Startup.Configure` `Map` メソッドとその他のクラスの静的メソッドがで呼び出され `UseEndpoints` ます。</span><span class="sxs-lookup"><span data-stu-id="7e979-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a><span data-ttu-id="7e979-161">Web API と比較した重要でない機能</span><span class="sxs-lookup"><span data-stu-id="7e979-161">Notable missing features compared to Web API</span></span>

<span data-ttu-id="7e979-162">Route-to-code は、基本的な JSON Api 向けに設計されています。</span><span class="sxs-lookup"><span data-stu-id="7e979-162">Route-to-code is designed for basic JSON APIs.</span></span> <span data-ttu-id="7e979-163">ASP.NET Core Web API によって提供される高度な機能の多くはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="7e979-163">It doesn't have support for many of the advanced features provided by ASP.NET Core Web API.</span></span>

<span data-ttu-id="7e979-164">によって提供されない機能 Route-to-code は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7e979-164">Features not provided by Route-to-code include:</span></span>

* <span data-ttu-id="7e979-165">モデル バインド</span><span class="sxs-lookup"><span data-stu-id="7e979-165">Model binding</span></span>
* <span data-ttu-id="7e979-166">モデルの検証</span><span class="sxs-lookup"><span data-stu-id="7e979-166">Model validation</span></span>
* <span data-ttu-id="7e979-167">API を開く/Swagger</span><span class="sxs-lookup"><span data-stu-id="7e979-167">OpenAPI/Swagger</span></span>
* <span data-ttu-id="7e979-168">コンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="7e979-168">Content negotiation</span></span>
* <span data-ttu-id="7e979-169">コンストラクターの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="7e979-169">Constructor dependency injection</span></span>
* <span data-ttu-id="7e979-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))</span><span class="sxs-lookup"><span data-stu-id="7e979-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))</span></span>

<span data-ttu-id="7e979-171">前の一覧の一部の機能が必要な場合は、 [ASP.NET Core WEB api](xref:web-api/index) を使用して API を作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="7e979-171">Consider using [ASP.NET Core web API](xref:web-api/index) to create an API if it requires some of the features in the preceding list.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e979-172">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7e979-172">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
