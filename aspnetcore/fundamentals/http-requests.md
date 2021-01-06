---
title: ASP.NET Core で IHttpClientFactory を使用して HTTP 要求を行う
author: stevejgordon
description: IHttpClientFactory インターフェイスを使用して、ASP.NET Core の論理 HttpClient インスタンスを管理する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
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
uid: fundamentals/http-requests
ms.openlocfilehash: 34c35daac3da845bac9156fe96078df7902a4cd0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059495"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="f1d07-103">ASP.NET Core で IHttpClientFactory を使用して HTTP 要求を行う</span><span class="sxs-lookup"><span data-stu-id="f1d07-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1d07-104">寄稿者: [Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="f1d07-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="f1d07-105">アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="f1d07-106">`IHttpClientFactory` には次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="f1d07-107">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-108">たとえば、[GitHub](https://github.com/) にアクセスするために、*github* という名前のクライアントを登録して構成できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="f1d07-109">一般的なアクセスのために、既定のクライアントを登録できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="f1d07-110">`HttpClient` でのハンドラーのデリゲートにより、送信ミドルウェアの概念が体系化されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="f1d07-111">`HttpClient` でのハンドラーのデリゲートを利用するために、Polly ベースのミドルウェアに対する拡張機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="f1d07-112">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="f1d07-113">自動管理により、`HttpClient` の有効期間を手動で管理するときの一般的な DNS (ドメイン ネーム システム) の問題が発生しなくなります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="f1d07-114">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f1d07-115">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f1d07-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f1d07-116">このトピックのバージョンのサンプル コードでは、HTTP 応答で返された JSON コンテンツを、<xref:System.Text.Json> を使用して逆シリアル化します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="f1d07-117">`Json.NET` および `ReadAsAsync<T>` を使用するサンプルについては、バージョン セレクターを使用して、このトピックの 2.x バージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="f1d07-118">利用パターン</span><span class="sxs-lookup"><span data-stu-id="f1d07-118">Consumption patterns</span></span>

<span data-ttu-id="f1d07-119">アプリで `IHttpClientFactory` を使用するには複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="f1d07-120">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="f1d07-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="f1d07-121">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="f1d07-122">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="f1d07-123">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="f1d07-124">どの方法が最善かは、アプリの要件によって異なります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="f1d07-125">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="f1d07-125">Basic usage</span></span>

<span data-ttu-id="f1d07-126">`IHttpClientFactory` は、`AddHttpClient` を呼び出すことによって登録できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="f1d07-127">`IHttpClientFactory` は、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用して要求できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f1d07-128">次のコードでは、`IHttpClientFactory` を使用して `HttpClient` インスタンスを作成しています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="f1d07-129">前の例のように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングするのに適した方法です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="f1d07-130">`HttpClient` の使用方法に対する影響はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="f1d07-131">既存のアプリで `HttpClient` のインスタンスが作成されている場所を、<xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="f1d07-132">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-132">Named clients</span></span>

<span data-ttu-id="f1d07-133">名前付きクライアントは、次の場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="f1d07-134">アプリで、多くの異なる `HttpClient` を使用する必要がある。</span><span class="sxs-lookup"><span data-stu-id="f1d07-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="f1d07-135">多くの `HttpClient` の構成が異なる。</span><span class="sxs-lookup"><span data-stu-id="f1d07-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="f1d07-136">名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="f1d07-137">上記のコードでは、クライアントに次のものが構成されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="f1d07-138">ベース アドレス `https://api.github.com/`。</span><span class="sxs-lookup"><span data-stu-id="f1d07-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="f1d07-139">GitHub API を使用するために必要な 2 つのヘッダー。</span><span class="sxs-lookup"><span data-stu-id="f1d07-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="f1d07-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="f1d07-140">CreateClient</span></span>

<span data-ttu-id="f1d07-141"><xref:System.Net.Http.IHttpClientFactory.CreateClient*> が呼び出されるたびに、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="f1d07-142">`HttpClient` の新しいインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="f1d07-143">構成アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-143">The configuration action is called.</span></span>

<span data-ttu-id="f1d07-144">名前付きクライアントを作成するには、その名前を `CreateClient` に渡します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="f1d07-145">上記のコードでは、要求でホスト名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="f1d07-146">クライアントに構成されているベース アドレスが使用されるため、コードではパスを渡すだけで済みます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="f1d07-147">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-147">Typed clients</span></span>

<span data-ttu-id="f1d07-148">型指定されたクライアント:</span><span class="sxs-lookup"><span data-stu-id="f1d07-148">Typed clients:</span></span>

* <span data-ttu-id="f1d07-149">キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="f1d07-150">クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="f1d07-151">特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="f1d07-152">たとえば、単一の型指定されたクライアントは、次のために使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="f1d07-153">単一のバックエンド エンドポイント用。</span><span class="sxs-lookup"><span data-stu-id="f1d07-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="f1d07-154">エンドポイントを処理するすべてのロジックをカプセル化するため。</span><span class="sxs-lookup"><span data-stu-id="f1d07-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="f1d07-155">DI に対応しており、アプリ内の必要な場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="f1d07-156">型指定されたクライアントは、そのコンストラクターで `HttpClient` パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f1d07-157">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-157">In the preceding code:</span></span>

* <span data-ttu-id="f1d07-158">構成が型指定されたクライアントに移動されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="f1d07-159">`HttpClient` オブジェクトは、パブリック プロパティとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="f1d07-160">`HttpClient` の機能を公開する API 固有のメソッドを作成できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="f1d07-161">たとえば、`GetAspNetDocsIssues` メソッドでは、未解決の問題を取得するためのコードがカプセル化されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="f1d07-162">次のコードでは、`Startup.ConfigureServices` 内で <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> を呼び出して、型指定されたクライアント クラスを登録しています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="f1d07-163">型指定されたクライアントは、DI で一時的として登録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="f1d07-164">上記のコードで、`AddHttpClient` は `GitHubService` を一時的なサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="f1d07-165">この登録では、ファクトリ メソッドを使用して次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="f1d07-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="f1d07-166">`HttpClient` のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="f1d07-167">`GitHubService` のインスタンスを作成し、`HttpClient` のインスタンスをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="f1d07-168">型指定されたクライアントは、直接挿入して使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="f1d07-169">型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="f1d07-170">`HttpClient` は、型指定されたクライアント内にカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="f1d07-171">プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="f1d07-172">上記のコードでは、`HttpClient` はプライベート フィールドに格納されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="f1d07-173">`HttpClient` へのアクセスは、パブリック `GetRepos` メソッドによって行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="f1d07-174">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-174">Generated clients</span></span>

<span data-ttu-id="f1d07-175">`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などのサードパーティ製ライブラリと組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="f1d07-176">Refit は、.NET 用の REST ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="f1d07-177">REST API をライブ インターフェイスに変換します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="f1d07-178">インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="f1d07-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="f1d07-179">インターフェイスと応答は、外部の API とその応答を表すように定義されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="f1d07-180">型指定されたクライアントを追加し、Refit を使用して実装を生成できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="f1d07-181">定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="f1d07-182">POST、PUT、DELETE の要求を行う</span><span class="sxs-lookup"><span data-stu-id="f1d07-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="f1d07-183">前の例では、すべての HTTP 要求で GET HTTP 動詞が使用されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="f1d07-184">`HttpClient` では、次のような他の HTTP 動詞もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="f1d07-185">POST</span><span class="sxs-lookup"><span data-stu-id="f1d07-185">POST</span></span>
* <span data-ttu-id="f1d07-186">PUT</span><span class="sxs-lookup"><span data-stu-id="f1d07-186">PUT</span></span>
* <span data-ttu-id="f1d07-187">Del</span><span class="sxs-lookup"><span data-stu-id="f1d07-187">DELETE</span></span>
* <span data-ttu-id="f1d07-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="f1d07-188">PATCH</span></span>

<span data-ttu-id="f1d07-189">サポートされている HTTP 動詞の一覧については、「<xref:System.Net.Http.HttpMethod>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="f1d07-190">次の例は、HTTP POST 要求の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="f1d07-191">上記のコードの `CreateItemAsync` メソッドは:</span><span class="sxs-lookup"><span data-stu-id="f1d07-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="f1d07-192">`System.Text.Json` を使用して `TodoItem` パラメーターを JSON にシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="f1d07-193">これは、<xref:System.Text.Json.JsonSerializerOptions> のインスタンスを使用して、シリアル化プロセスを構成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="f1d07-194">HTTP 要求の本文で送信するためにシリアル化された JSON をパッケージ化する <xref:System.Net.Http.StringContent> のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="f1d07-195"><xref:System.Net.Http.HttpClient.PostAsync%2A> を呼び出して、指定した URL に JSON の内容を送信します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="f1d07-196">これは [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) に追加される相対 URL です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="f1d07-197">応答状態コードが成功を示していない場合に、<xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> を呼び出して例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="f1d07-198">`HttpClient` は、他の種類のコンテンツもサポートしています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="f1d07-199">たとえば、<xref:System.Net.Http.MultipartContent> と<xref:System.Net.Http.StreamContent> です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="f1d07-200">サポートされているコンテンツの一覧については、「<xref:System.Net.Http.HttpContent>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="f1d07-201">HTTP PUT 要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="f1d07-202">上記のコードは、POST の例によく似ています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="f1d07-203">`SaveItemAsync` メソッドは `PostAsync` ではなく <xref:System.Net.Http.HttpClient.PutAsync%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="f1d07-204">HTTP DELETE 要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="f1d07-205">上記のコードの `DeleteItemAsync` メソッドは <xref:System.Net.Http.HttpClient.DeleteAsync%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="f1d07-206">HTTP DELETE 要求には通常、本文が含まれていないため、`DeleteAsync` メソッドは `HttpContent` のインスタンスを受け入れるオーバーロードを提供しません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="f1d07-207">`HttpClient` でのさまざまな HTTP 動詞の使用の詳細については、「<xref:System.Net.Http.HttpClient>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="f1d07-208">送信要求ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f1d07-208">Outgoing request middleware</span></span>

<span data-ttu-id="f1d07-209">`HttpClient` は、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="f1d07-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="f1d07-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="f1d07-211">各名前付きクライアントに適用するハンドラーの定義が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="f1d07-212">送信要求ミドルウェア パイプラインを構築するための複数のハンドラーの登録とチェーン化がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f1d07-213">各ハンドラーは、送信要求の前と後に処理を実行できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="f1d07-214">このパターンは次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-214">This pattern:</span></span>

  * <span data-ttu-id="f1d07-215">ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="f1d07-216">次のような HTTP 要求に関する横断的な問題を管理するためのメカニズムが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="f1d07-217">キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f1d07-217">caching</span></span>
    * <span data-ttu-id="f1d07-218">エラー処理</span><span class="sxs-lookup"><span data-stu-id="f1d07-218">error handling</span></span>
    * <span data-ttu-id="f1d07-219">シリアル化</span><span class="sxs-lookup"><span data-stu-id="f1d07-219">serialization</span></span>
    * <span data-ttu-id="f1d07-220">ログ</span><span class="sxs-lookup"><span data-stu-id="f1d07-220">logging</span></span>

<span data-ttu-id="f1d07-221">デリゲート ハンドラーを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-221">To create a delegating handler:</span></span>

* <span data-ttu-id="f1d07-222"><xref:System.Net.Http.DelegatingHandler> から派生します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="f1d07-223"><xref:System.Net.Http.DelegatingHandler.SendAsync*> をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="f1d07-224">パイプライン内の次のハンドラーに要求を渡す前に、コードを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="f1d07-225">上記のコードでは、`X-API-KEY` ヘッダーが要求に含まれているかどうかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="f1d07-226">`X-API-KEY` がない場合は、<xref:System.Net.HttpStatusCode.BadRequest> が返されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="f1d07-227"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> を使用して `HttpClient` の構成に複数のハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="f1d07-228">上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="f1d07-229">`IHttpClientFactory` では、ハンドラーごとに個別の DI スコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="f1d07-230">ハンドラーは、任意のスコープのサービスに依存することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="f1d07-231">ハンドラーが依存するサービスは、ハンドラーが破棄されるときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="f1d07-232">登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="f1d07-233">実行する順序で、複数のハンドラーを登録することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="f1d07-234">最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="f1d07-235">次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="f1d07-236">[HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) を使用して、ハンドラーにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="f1d07-237"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> を使って現在の要求にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="f1d07-238">データを渡すカスタムの <xref:System.Threading.AsyncLocal`1> ストレージ オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="f1d07-239">Polly ベースのハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="f1d07-239">Use Polly-based handlers</span></span>

<span data-ttu-id="f1d07-240">`IHttpClientFactory` は、サードパーティのライブラリ [Polly](https://github.com/App-vNext/Polly) と統合します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="f1d07-241">Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="f1d07-242">開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="f1d07-243">構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-244">Polly の拡張機能では、クライアントへの Polly ベースのハンドラーの追加がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="f1d07-245">Polly では、[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="f1d07-246">一時的な障害を処理する</span><span class="sxs-lookup"><span data-stu-id="f1d07-246">Handle transient faults</span></span>

<span data-ttu-id="f1d07-247">通常、外部 HTTP を呼び出すときに発生する障害は、一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="f1d07-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> では、一時的なエラーを処理するためのポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="f1d07-249">`AddTransientHttpErrorPolicy` で構成されたポリシーでは、次の応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="f1d07-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="f1d07-250">HTTP 5xx</span></span>
* <span data-ttu-id="f1d07-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="f1d07-251">HTTP 408</span></span>

<span data-ttu-id="f1d07-252">`AddTransientHttpErrorPolicy` では、可能性のある一時的障害を表すエラーを処理するために構成された `PolicyBuilder` オブジェクトへのアクセスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="f1d07-253">上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="f1d07-254">失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="f1d07-255">ポリシーを動的に選択する</span><span class="sxs-lookup"><span data-stu-id="f1d07-255">Dynamically select policies</span></span>

<span data-ttu-id="f1d07-256">Polly ベースのハンドラーを追加するための拡張メソッドが提供されています (<xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> など)。</span><span class="sxs-lookup"><span data-stu-id="f1d07-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="f1d07-257">次の `AddPolicyHandler` のオーバーロードでは、要求が検査されて、適用するポリシーが決定されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="f1d07-258">上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="f1d07-259">他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="f1d07-260">複数の Polly ハンドラーを追加する</span><span class="sxs-lookup"><span data-stu-id="f1d07-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="f1d07-261">Polly のポリシーは入れ子にするのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="f1d07-262">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="f1d07-262">In the preceding example:</span></span>

* <span data-ttu-id="f1d07-263">2 つのハンドラーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-263">Two handlers are added.</span></span>
* <span data-ttu-id="f1d07-264">1 つ目のハンドラーでは、<xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> を使用して再試行ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="f1d07-265">失敗した要求は 3 回まで再試行されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="f1d07-266">2 つ目の `AddTransientHttpErrorPolicy` の呼び出しでは、サーキット ブレーカー ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="f1d07-267">試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="f1d07-268">サーキット ブレーカー ポリシーはステートフルです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="f1d07-269">このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="f1d07-270">Polly レジストリからポリシーを追加する</span><span class="sxs-lookup"><span data-stu-id="f1d07-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="f1d07-271">定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="f1d07-272">次のコードの内容は以下のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-272">In the following code:</span></span>

* <span data-ttu-id="f1d07-273">"regular" ポリシーと "long" ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="f1d07-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> では、レジストリから "regular" ポリシーと "long" ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="f1d07-275">`IHttpClientFactory` と Polly の統合の詳細については、[Polly に関する wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="f1d07-276">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="f1d07-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="f1d07-277">`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="f1d07-278"><xref:System.Net.Http.HttpMessageHandler> は、名前付きクライアントごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="f1d07-279">ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="f1d07-280">`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="f1d07-281">新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="f1d07-282">通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="f1d07-283">必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="f1d07-284">また、一部のハンドラーでは接続が無期限に開かれており、DNS (ドメイン ネーム システム) の変更にハンドラーが対応できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="f1d07-285">ハンドラーの既定の有効期間は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="f1d07-286">名前付きクライアントごとに、既定値をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="f1d07-287">通常、`HttpClient` インスタンスは、破棄する必要の **ない** .NET オブジェクトとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="f1d07-288">破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="f1d07-289">`IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="f1d07-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="f1d07-290">`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="f1d07-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="f1d07-291">`IHttpClientFactory` に移行した後は、このパターンは不要になります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="f1d07-292">IHttpClientFactory の代替手段</span><span class="sxs-lookup"><span data-stu-id="f1d07-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="f1d07-293">DI 対応のアプリ内で `IHttpClientFactory` を使用すれば、次のことを回避できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="f1d07-294">`HttpMessageHandler` インスタンスをプールすることによるリソース枯渇の問題。</span><span class="sxs-lookup"><span data-stu-id="f1d07-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="f1d07-295">一定の間隔で `HttpMessageHandler` インスタンスを循環させることによって発生する古くなった DNS の問題。</span><span class="sxs-lookup"><span data-stu-id="f1d07-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="f1d07-296">有効期間の長い <xref:System.Net.Http.SocketsHttpHandler> インスタンスを使用して、上記の問題を解決する別の方法があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="f1d07-297">アプリの起動時に `SocketsHttpHandler` インスタンスを作成し、アプリの有効期間中、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="f1d07-298">DNS の更新時間に基づいて、<xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> を適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="f1d07-299">必要に応じて `new HttpClient(handler, disposeHandler: false)` を使用して `HttpClient` インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="f1d07-300">上記の方法を使用すると、`IHttpClientFactory` が同様の方法で解決するリソース管理の問題を解決できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="f1d07-301">`SocketsHttpHandler` を使用すると、`HttpClient` インスタンス間で接続を共有できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-302">この共有によってソケットの枯渇が防止されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="f1d07-303">`SocketsHttpHandler` では、古くなった DNS の問題を回避するために `PooledConnectionLifetime` に従って接続を循環されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="f1d07-304">Cookies</span><span class="sxs-lookup"><span data-stu-id="f1d07-304">Cookies</span></span>

<span data-ttu-id="f1d07-305">`HttpMessageHandler` インスタンスをプールすると、`CookieContainer` オブジェクトが共有されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="f1d07-306">予期せぬ `CookieContainer` オブジェクト共有があると、多くの場合、コードは不適切なものとなります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="f1d07-307">cookie を必要とするアプリの場合は、次のいずれかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="f1d07-308">自動的な cookie 処理の無効化</span><span class="sxs-lookup"><span data-stu-id="f1d07-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="f1d07-309">`IHttpClientFactory` の回避</span><span class="sxs-lookup"><span data-stu-id="f1d07-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="f1d07-310"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> を呼び出して、自動的な cookie 処理を無効にします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="f1d07-311">ログの記録</span><span class="sxs-lookup"><span data-stu-id="f1d07-311">Logging</span></span>

<span data-ttu-id="f1d07-312">`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="f1d07-313">既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="f1d07-314">要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="f1d07-315">各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="f1d07-316">たとえば、*MyNamedClient* という名前のクライアントでは、"System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler" というカテゴリでメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="f1d07-317">*LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="f1d07-318">要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="f1d07-319">応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="f1d07-320">ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="f1d07-321">*MyNamedClient* の例では、これらのメッセージはログ カテゴリ "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler" でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="f1d07-322">要求では、他のすべてのハンドラーが実行された後、要求が送信される直前に、これが行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="f1d07-323">応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="f1d07-324">パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="f1d07-325">これには、要求ヘッダーや応答状態コードへの変更を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="f1d07-326">ログのカテゴリにクライアントの名前を含めると、特定の名前付きクライアントでログをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="f1d07-327">HttpMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="f1d07-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="f1d07-328">クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="f1d07-329">名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="f1d07-330"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="f1d07-331">デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="f1d07-332">コンソール アプリで IHttpClientFactory を使用する</span><span class="sxs-lookup"><span data-stu-id="f1d07-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="f1d07-333">コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="f1d07-334">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="f1d07-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="f1d07-335">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="f1d07-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="f1d07-336">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-336">In the following example:</span></span>

* <span data-ttu-id="f1d07-337"><xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="f1d07-338">`MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="f1d07-339">`HttpClient` は Web ページを取得する目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="f1d07-340">`Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="f1d07-341">ヘッダー伝達ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f1d07-341">Header propagation middleware</span></span>

<span data-ttu-id="f1d07-342">ヘッダー伝達は、受信要求から送信 HTTP クライアント要求に HTTP ヘッダーを伝達するための ASP.NET Core ミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="f1d07-343">ヘッダー伝達を使用するには、次を行います。</span><span class="sxs-lookup"><span data-stu-id="f1d07-343">To use header propagation:</span></span>

* <span data-ttu-id="f1d07-344">[Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="f1d07-345">`Startup` でミドルウェアと `HttpClient` を構成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="f1d07-346">クライアントで、構成されたヘッダーが送信要求に含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="f1d07-347">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f1d07-347">Additional resources</span></span>

* [<span data-ttu-id="f1d07-348">HttpClientFactory を使用して回復力の高い HTTP 要求を実装する</span><span class="sxs-lookup"><span data-stu-id="f1d07-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="f1d07-349">HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する</span><span class="sxs-lookup"><span data-stu-id="f1d07-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="f1d07-350">サーキット ブレーカー パターンを実装する</span><span class="sxs-lookup"><span data-stu-id="f1d07-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="f1d07-351">.NET で JSON のシリアル化と逆シリアル化を行う方法</span><span class="sxs-lookup"><span data-stu-id="f1d07-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f1d07-352">寄稿者: [Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="f1d07-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="f1d07-353">アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="f1d07-354">次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-354">It offers the following benefits:</span></span>

* <span data-ttu-id="f1d07-355">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-356">たとえば、*github* クライアントを登録して、[GitHub](https://github.com/) にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="f1d07-357">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="f1d07-358">`HttpClient` でのハンドラーのデリゲートにより送信ミドルウェアの概念を体系化し、Polly ベースのミドルウェアでそれを利用するための拡張機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="f1d07-359">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="f1d07-360">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f1d07-361">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f1d07-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="f1d07-362">利用パターン</span><span class="sxs-lookup"><span data-stu-id="f1d07-362">Consumption patterns</span></span>

<span data-ttu-id="f1d07-363">アプリで `IHttpClientFactory` を使用するには複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="f1d07-364">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="f1d07-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="f1d07-365">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="f1d07-366">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="f1d07-367">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="f1d07-368">これらの間に厳密な優劣はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="f1d07-369">どの方法が最善かは、アプリの制約に依存します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="f1d07-370">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="f1d07-370">Basic usage</span></span>

<span data-ttu-id="f1d07-371">`IHttpClientFactory` は、`Startup.ConfigureServices` メソッドの内部で `IServiceCollection` の `AddHttpClient` 拡張メソッドを呼び出すことによって登録できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="f1d07-372">登録が済むと、コードは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用してサービスを挿入できる任意の場所で、`IHttpClientFactory` を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f1d07-373">`IHttpClientFactory` を使用して、`HttpClient` インスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="f1d07-374">このように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングする優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="f1d07-375">`HttpClient` の使用方法に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="f1d07-376">現在 `HttpClient` インスタンスが作成されている場所で、それを <xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="f1d07-377">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-377">Named clients</span></span>

<span data-ttu-id="f1d07-378">それぞれ構成が異なる多数の `HttpClient` をアプリで個別に使用する必要がある場合は、**名前付きクライアント** を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="f1d07-379">名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="f1d07-380">上記のコードでは、*github* という名前を指定して `AddHttpClient` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="f1d07-381">このクライアントには既定の構成がいくつか適用されています。つまり、GitHub API を使用するために必要なベース アドレスと 2 つのヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="f1d07-382">`CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが作成されて、構成アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="f1d07-383">名前付きクライアントを使用するには、文字列パラメーターを `CreateClient` に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="f1d07-384">作成するクライアントの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="f1d07-385">上記のコードでは、要求でホスト名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="f1d07-386">クライアントに構成されているベース アドレスが使用されるため、パスだけを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="f1d07-387">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-387">Typed clients</span></span>

<span data-ttu-id="f1d07-388">型指定されたクライアント:</span><span class="sxs-lookup"><span data-stu-id="f1d07-388">Typed clients:</span></span>

* <span data-ttu-id="f1d07-389">キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="f1d07-390">クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="f1d07-391">特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="f1d07-392">たとえば、単一の型指定されたクライアントを単一のバックエンド エンドポイントに対して使用し、そのエンドポイントを操作するすべてのロジックをカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="f1d07-393">DI に対応しており、アプリ内の必要な場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="f1d07-394">型指定されたクライアントは、そのコンストラクターで `HttpClient` パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="f1d07-395">上記のコードでは、構成が型指定されたクライアントに移動されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="f1d07-396">`HttpClient` オブジェクトは、パブリック プロパティとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="f1d07-397">`HttpClient` 機能を公開する API 固有のメソッドを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="f1d07-398">`GetAspNetDocsIssues` メソッドは、GitHub リポジトリで最新の未解決の問題をクエリして解析するために必要なコードをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="f1d07-399">型指定されたクライアントを登録するには、ジェネリック <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 拡張メソッドを `Startup.ConfigureServices` 内で使用して、型指定されたクライアントのクラスを指定します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="f1d07-400">型指定されたクライアントは、DI で一時的として登録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="f1d07-401">型指定されたクライアントは、直接挿入して使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="f1d07-402">好みに応じて、型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="f1d07-403">型指定されたクライアントの内部に `HttpClient` を完全にカプセル化することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="f1d07-404">プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドとして提供することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="f1d07-405">上記のコードでは、`HttpClient` はプライベート フィールドとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="f1d07-406">外部呼び出しを行うすべてのアクセスは、`GetRepos` メソッドを通して行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="f1d07-407">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-407">Generated clients</span></span>

<span data-ttu-id="f1d07-408">`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などの他のサードパーティ製ライブラリと組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="f1d07-409">Refit は、.NET 用の REST ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="f1d07-410">REST API をライブ インターフェイスに変換します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="f1d07-411">インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="f1d07-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="f1d07-412">インターフェイスと応答は、外部の API とその応答を表すように定義されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-412">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="f1d07-413">型指定されたクライアントを追加し、Refit を使用して実装を生成できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-413">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="f1d07-414">定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="f1d07-415">送信要求ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f1d07-415">Outgoing request middleware</span></span>

<span data-ttu-id="f1d07-416">`HttpClient` は既に、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="f1d07-417">`IHttpClientFactory` を使用すると、各名前付きクライアントに適用するハンドラーを簡単に定義できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="f1d07-418">複数のハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f1d07-419">各ハンドラーは、送信要求の前と後に処理を実行できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="f1d07-420">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="f1d07-421">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="f1d07-422">ハンドラーを作成するには、<xref:System.Net.Http.DelegatingHandler> の派生クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="f1d07-423">パイプライン内の次のハンドラーに要求を渡す前にコードを実行するように、`SendAsync` メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="f1d07-424">上記のコードでは、基本的なハンドラーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="f1d07-425">このコードは、`X-API-KEY` ヘッダーが要求に含まれていたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="f1d07-426">ヘッダーがない場合、HTTP 呼び出しを行わずに適切な応答を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="f1d07-427">登録時には、1 つまたは複数のハンドラーを `HttpClient` の構成に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="f1d07-428">このタスクは、<xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> の拡張メソッドを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="f1d07-429">上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="f1d07-430">`IHttpClientFactory` では、ハンドラーごとに個別の DI スコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="f1d07-431">ハンドラーは、任意のスコープのサービスに自由に依存することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="f1d07-432">ハンドラーが依存するサービスは、ハンドラーが破棄されるときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="f1d07-433">登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="f1d07-434">実行する順序で、複数のハンドラーを登録することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="f1d07-435">最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="f1d07-436">次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="f1d07-437">`HttpRequestMessage.Properties` を使ってデータをハンドラーに渡します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="f1d07-438">`IHttpContextAccessor` を使って現在の要求にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="f1d07-439">データを渡すカスタムの `AsyncLocal` ストレージ オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="f1d07-440">Polly ベースのハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="f1d07-440">Use Polly-based handlers</span></span>

<span data-ttu-id="f1d07-441">`IHttpClientFactory` は、[Polly](https://github.com/App-vNext/Polly) という名前の人気のあるサードパーティ製ライブラリと統合します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="f1d07-442">Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="f1d07-443">開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="f1d07-444">構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-445">Polly の拡張機能は、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-445">The Polly extensions:</span></span>

* <span data-ttu-id="f1d07-446">クライアントへの Polly ベースのハンドラーの追加がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="f1d07-447">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージのインストール後に使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="f1d07-448">このパッケージは、ASP.NET Core 共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="f1d07-449">一時的な障害を処理する</span><span class="sxs-lookup"><span data-stu-id="f1d07-449">Handle transient faults</span></span>

<span data-ttu-id="f1d07-450">外部 HTTP を呼び出す際に発生する一般的な障害のほとんどは、一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="f1d07-451">`AddTransientHttpErrorPolicy` という便利な拡張メソッドが含まれており、一時的なエラーを処理するためのポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="f1d07-452">この拡張メソッドで構成されたポリシーは、`HttpRequestException`、HTTP 5xx 応答、および HTTP 408 応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="f1d07-453">`AddTransientHttpErrorPolicy` 拡張メソッドは、`Startup.ConfigureServices` 内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f1d07-454">この拡張メソッドは、可能性のある一時的障害を表すエラーを処理するように構成された `PolicyBuilder` オブジェクトへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="f1d07-455">上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="f1d07-456">失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="f1d07-457">ポリシーを動的に選択する</span><span class="sxs-lookup"><span data-stu-id="f1d07-457">Dynamically select policies</span></span>

<span data-ttu-id="f1d07-458">Polly ベースのハンドラーを追加するために使用できる追加の拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="f1d07-459">そのような拡張メソッドの 1 つは `AddPolicyHandler` であり、複数のオーバーロードを持ちます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="f1d07-460">1 つのオーバーロードでは、適用するポリシーを定義するときに要求を検査できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="f1d07-461">上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="f1d07-462">他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="f1d07-463">複数の Polly ハンドラーを追加する</span><span class="sxs-lookup"><span data-stu-id="f1d07-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="f1d07-464">Polly ポリシーを入れ子にして拡張機能を提供するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="f1d07-465">前の例では、2 つのハンドラーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="f1d07-466">最初のハンドラーは、`AddTransientHttpErrorPolicy` 拡張メソッドを使用して再試行ポリシーを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="f1d07-467">失敗した要求は 3 回まで再試行されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="f1d07-468">`AddTransientHttpErrorPolicy` の 2 番目の呼び出しでは、サーキット ブレーカー ポリシーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="f1d07-469">試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="f1d07-470">サーキット ブレーカー ポリシーはステートフルです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="f1d07-471">このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="f1d07-472">Polly レジストリからポリシーを追加する</span><span class="sxs-lookup"><span data-stu-id="f1d07-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="f1d07-473">定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="f1d07-474">提供されている拡張メソッドを使用することで、レジストリからポリシーを使用してハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="f1d07-475">上記のコードでは、`PolicyRegistry` が `ServiceCollection` に追加されるときに 2 つのポリシーが登録されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="f1d07-476">レジストリからポリシーを使用するには、適用するポリシーの名前を渡して `AddPolicyHandlerFromRegistry` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="f1d07-477">`IHttpClientFactory` および Polly の統合について詳しくは、[Polly の Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="f1d07-478">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="f1d07-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="f1d07-479">`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="f1d07-480">名前付きクライアントごとに <xref:System.Net.Http.HttpMessageHandler> が存在します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="f1d07-481">ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="f1d07-482">`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="f1d07-483">新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="f1d07-484">通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="f1d07-485">必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="f1d07-486">また、一部のハンドラーは接続を無期限に開いており、DNS の変更にハンドラーが対応できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="f1d07-487">ハンドラーの既定の有効期間は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="f1d07-488">名前付きクライアントごとに、既定値をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="f1d07-489">オーバーライドするには、クライアント作成時に返された `IHttpClientBuilder` で <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="f1d07-490">クライアントを破棄する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="f1d07-491">破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="f1d07-492">`IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="f1d07-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-493">通常、`HttpClient` インスタンスは、破棄を必要としない .NET オブジェクトとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="f1d07-494">`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="f1d07-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="f1d07-495">`IHttpClientFactory` に移行した後は、このパターンは不要になります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="f1d07-496">IHttpClientFactory の代替手段</span><span class="sxs-lookup"><span data-stu-id="f1d07-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="f1d07-497">DI 対応のアプリ内で `IHttpClientFactory` を使用すれば、次のことを回避できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="f1d07-498">`HttpMessageHandler` インスタンスをプールすることによるリソース枯渇の問題。</span><span class="sxs-lookup"><span data-stu-id="f1d07-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="f1d07-499">一定の間隔で `HttpMessageHandler` インスタンスを循環させることによって発生する古くなった DNS の問題。</span><span class="sxs-lookup"><span data-stu-id="f1d07-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="f1d07-500">有効期間の長い <xref:System.Net.Http.SocketsHttpHandler> インスタンスを使用して、上記の問題を解決する別の方法があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="f1d07-501">アプリの起動時に `SocketsHttpHandler` インスタンスを作成し、アプリの有効期間中、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="f1d07-502">DNS の更新時間に基づいて、<xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> を適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="f1d07-503">必要に応じて `new HttpClient(handler, disposeHandler: false)` を使用して `HttpClient` インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="f1d07-504">上記の方法を使用すると、`IHttpClientFactory` が同様の方法で解決するリソース管理の問題を解決できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="f1d07-505">`SocketsHttpHandler` を使用すると、`HttpClient` インスタンス間で接続を共有できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-506">この共有によってソケットの枯渇が防止されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="f1d07-507">`SocketsHttpHandler` では、古くなった DNS の問題を回避するために `PooledConnectionLifetime` に従って接続を循環されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="f1d07-508">Cookies</span><span class="sxs-lookup"><span data-stu-id="f1d07-508">Cookies</span></span>

<span data-ttu-id="f1d07-509">`HttpMessageHandler` インスタンスをプールすると、`CookieContainer` オブジェクトが共有されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="f1d07-510">予期せぬ `CookieContainer` オブジェクト共有があると、多くの場合、コードは不適切なものとなります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="f1d07-511">cookie を必要とするアプリの場合は、次のいずれかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="f1d07-512">自動的な cookie 処理の無効化</span><span class="sxs-lookup"><span data-stu-id="f1d07-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="f1d07-513">`IHttpClientFactory` の回避</span><span class="sxs-lookup"><span data-stu-id="f1d07-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="f1d07-514"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> を呼び出して、自動的な cookie 処理を無効にします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="f1d07-515">ログの記録</span><span class="sxs-lookup"><span data-stu-id="f1d07-515">Logging</span></span>

<span data-ttu-id="f1d07-516">`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="f1d07-517">既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="f1d07-518">要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="f1d07-519">各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="f1d07-520">たとえば、*MyNamedClient* という名前のクライアントは、カテゴリ `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` でメッセージをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="f1d07-521">*LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="f1d07-522">要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="f1d07-523">応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="f1d07-524">ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="f1d07-525">*MyNamedClient* の例では、それらのメッセージはログ カテゴリ `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` に対して記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="f1d07-526">要求では、他のすべてのハンドラーが実行した後、要求がネットワークに送信される直前に、これが行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="f1d07-527">応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="f1d07-528">パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="f1d07-529">これには、たとえば、要求ヘッダーや応答状態コードへの変更を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="f1d07-530">ログのカテゴリにクライアントの名前を含めると、必要に応じて、特定の名前付きクライアントでログをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="f1d07-531">HttpMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="f1d07-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="f1d07-532">クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="f1d07-533">名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="f1d07-534"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="f1d07-535">デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="f1d07-536">コンソール アプリで IHttpClientFactory を使用する</span><span class="sxs-lookup"><span data-stu-id="f1d07-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="f1d07-537">コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="f1d07-538">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="f1d07-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="f1d07-539">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="f1d07-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="f1d07-540">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-540">In the following example:</span></span>

* <span data-ttu-id="f1d07-541"><xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="f1d07-542">`MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="f1d07-543">`HttpClient` は Web ページを取得する目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="f1d07-544">`Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="f1d07-545">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f1d07-545">Additional resources</span></span>

* [<span data-ttu-id="f1d07-546">HttpClientFactory を使用して回復力の高い HTTP 要求を実装する</span><span class="sxs-lookup"><span data-stu-id="f1d07-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="f1d07-547">HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する</span><span class="sxs-lookup"><span data-stu-id="f1d07-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="f1d07-548">サーキット ブレーカー パターンを実装する</span><span class="sxs-lookup"><span data-stu-id="f1d07-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f1d07-549">寄稿者: [Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="f1d07-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="f1d07-550">アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="f1d07-551">次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-551">It offers the following benefits:</span></span>

* <span data-ttu-id="f1d07-552">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-553">たとえば、*github* クライアントを登録して、[GitHub](https://github.com/) にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="f1d07-554">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="f1d07-555">`HttpClient` でのハンドラーのデリゲートにより送信ミドルウェアの概念を体系化し、Polly ベースのミドルウェアでそれを利用するための拡張機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="f1d07-556">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="f1d07-557">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f1d07-558">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f1d07-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1d07-559">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f1d07-559">Prerequisites</span></span>

<span data-ttu-id="f1d07-560">.NET Framework をターゲットとするプロジェクトでは、[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet パッケージのインストールが必要です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="f1d07-561">.NET Core をターゲットとし、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するプロジェクトには、既に `Microsoft.Extensions.Http` パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="f1d07-562">利用パターン</span><span class="sxs-lookup"><span data-stu-id="f1d07-562">Consumption patterns</span></span>

<span data-ttu-id="f1d07-563">アプリで `IHttpClientFactory` を使用するには複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="f1d07-564">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="f1d07-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="f1d07-565">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="f1d07-566">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="f1d07-567">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="f1d07-568">これらの間に厳密な優劣はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="f1d07-569">どの方法が最善かは、アプリの制約に依存します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="f1d07-570">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="f1d07-570">Basic usage</span></span>

<span data-ttu-id="f1d07-571">`IHttpClientFactory` は、`Startup.ConfigureServices` メソッドの内部で `IServiceCollection` の `AddHttpClient` 拡張メソッドを呼び出すことによって登録できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="f1d07-572">登録が済むと、コードは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用してサービスを挿入できる任意の場所で、`IHttpClientFactory` を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f1d07-573">`IHttpClientFactory` を使用して、`HttpClient` インスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="f1d07-574">このように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングする優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="f1d07-575">`HttpClient` の使用方法に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="f1d07-576">現在 `HttpClient` インスタンスが作成されている場所で、それを <xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="f1d07-577">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-577">Named clients</span></span>

<span data-ttu-id="f1d07-578">それぞれ構成が異なる多数の `HttpClient` をアプリで個別に使用する必要がある場合は、**名前付きクライアント** を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="f1d07-579">名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="f1d07-580">上記のコードでは、*github* という名前を指定して `AddHttpClient` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="f1d07-581">このクライアントには既定の構成がいくつか適用されています。つまり、GitHub API を使用するために必要なベース アドレスと 2 つのヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="f1d07-582">`CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが作成されて、構成アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="f1d07-583">名前付きクライアントを使用するには、文字列パラメーターを `CreateClient` に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="f1d07-584">作成するクライアントの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="f1d07-585">上記のコードでは、要求でホスト名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="f1d07-586">クライアントに構成されているベース アドレスが使用されるため、パスだけを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="f1d07-587">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-587">Typed clients</span></span>

<span data-ttu-id="f1d07-588">型指定されたクライアント:</span><span class="sxs-lookup"><span data-stu-id="f1d07-588">Typed clients:</span></span>

* <span data-ttu-id="f1d07-589">キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="f1d07-590">クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="f1d07-591">特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="f1d07-592">たとえば、単一の型指定されたクライアントを単一のバックエンド エンドポイントに対して使用し、そのエンドポイントを操作するすべてのロジックをカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="f1d07-593">DI に対応しており、アプリ内の必要な場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="f1d07-594">型指定されたクライアントは、そのコンストラクターで `HttpClient` パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="f1d07-595">上記のコードでは、構成が型指定されたクライアントに移動されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="f1d07-596">`HttpClient` オブジェクトは、パブリック プロパティとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="f1d07-597">`HttpClient` 機能を公開する API 固有のメソッドを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="f1d07-598">`GetAspNetDocsIssues` メソッドは、GitHub リポジトリで最新の未解決の問題をクエリして解析するために必要なコードをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="f1d07-599">型指定されたクライアントを登録するには、ジェネリック <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 拡張メソッドを `Startup.ConfigureServices` 内で使用して、型指定されたクライアントのクラスを指定します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="f1d07-600">型指定されたクライアントは、DI で一時的として登録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="f1d07-601">型指定されたクライアントは、直接挿入して使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="f1d07-602">好みに応じて、型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="f1d07-603">型指定されたクライアントの内部に `HttpClient` を完全にカプセル化することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="f1d07-604">プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドとして提供することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="f1d07-605">上記のコードでは、`HttpClient` はプライベート フィールドとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="f1d07-606">外部呼び出しを行うすべてのアクセスは、`GetRepos` メソッドを通して行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="f1d07-607">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="f1d07-607">Generated clients</span></span>

<span data-ttu-id="f1d07-608">`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などの他のサードパーティ製ライブラリと組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="f1d07-609">Refit は、.NET 用の REST ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="f1d07-610">REST API をライブ インターフェイスに変換します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="f1d07-611">インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="f1d07-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="f1d07-612">インターフェイスと応答は、外部の API とその応答を表すように定義されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-612">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="f1d07-613">型指定されたクライアントを追加し、Refit を使用して実装を生成できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-613">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="f1d07-614">定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="f1d07-615">送信要求ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f1d07-615">Outgoing request middleware</span></span>

<span data-ttu-id="f1d07-616">`HttpClient` は既に、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="f1d07-617">`IHttpClientFactory` を使用すると、各名前付きクライアントに適用するハンドラーを簡単に定義できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="f1d07-618">複数のハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f1d07-619">各ハンドラーは、送信要求の前と後に処理を実行できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="f1d07-620">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="f1d07-621">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="f1d07-622">ハンドラーを作成するには、<xref:System.Net.Http.DelegatingHandler> の派生クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="f1d07-623">パイプライン内の次のハンドラーに要求を渡す前にコードを実行するように、`SendAsync` メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="f1d07-624">上記のコードでは、基本的なハンドラーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="f1d07-625">このコードは、`X-API-KEY` ヘッダーが要求に含まれていたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="f1d07-626">ヘッダーがない場合、HTTP 呼び出しを行わずに適切な応答を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="f1d07-627">登録時には、1 つまたは複数のハンドラーを `HttpClient` の構成に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="f1d07-628">このタスクは、<xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> の拡張メソッドを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="f1d07-629">上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="f1d07-630">ハンドラーは、スコープではなく、一時的なサービスとして DI で登録する **必要があります**。</span><span class="sxs-lookup"><span data-stu-id="f1d07-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="f1d07-631">ハンドラーがスコープ付きサービスとして登録されており、ハンドラーが依存するサービスを破棄できる場合:</span><span class="sxs-lookup"><span data-stu-id="f1d07-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="f1d07-632">ハンドラーのサービスは、ハンドラーがスコープ外に出る前に破棄されることがあります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="f1d07-633">破棄されたハンドラー サービスが原因でハンドラーが失敗します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="f1d07-634">登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="f1d07-635">実行する順序で、複数のハンドラーを登録することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="f1d07-636">最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="f1d07-637">次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="f1d07-638">`HttpRequestMessage.Properties` を使ってデータをハンドラーに渡します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="f1d07-639">`IHttpContextAccessor` を使って現在の要求にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="f1d07-640">データを渡すカスタムの `AsyncLocal` ストレージ オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="f1d07-641">Polly ベースのハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="f1d07-641">Use Polly-based handlers</span></span>

<span data-ttu-id="f1d07-642">`IHttpClientFactory` は、[Polly](https://github.com/App-vNext/Polly) という名前の人気のあるサードパーティ製ライブラリと統合します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="f1d07-643">Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="f1d07-644">開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="f1d07-645">構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-646">Polly の拡張機能は、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-646">The Polly extensions:</span></span>

* <span data-ttu-id="f1d07-647">クライアントへの Polly ベースのハンドラーの追加がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="f1d07-648">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージのインストール後に使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="f1d07-649">このパッケージは、ASP.NET Core 共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="f1d07-650">一時的な障害を処理する</span><span class="sxs-lookup"><span data-stu-id="f1d07-650">Handle transient faults</span></span>

<span data-ttu-id="f1d07-651">外部 HTTP を呼び出す際に発生する一般的な障害のほとんどは、一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="f1d07-652">`AddTransientHttpErrorPolicy` という便利な拡張メソッドが含まれており、一時的なエラーを処理するためのポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="f1d07-653">この拡張メソッドで構成されたポリシーは、`HttpRequestException`、HTTP 5xx 応答、および HTTP 408 応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="f1d07-654">`AddTransientHttpErrorPolicy` 拡張メソッドは、`Startup.ConfigureServices` 内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f1d07-655">この拡張メソッドは、可能性のある一時的障害を表すエラーを処理するように構成された `PolicyBuilder` オブジェクトへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="f1d07-656">上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="f1d07-657">失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="f1d07-658">ポリシーを動的に選択する</span><span class="sxs-lookup"><span data-stu-id="f1d07-658">Dynamically select policies</span></span>

<span data-ttu-id="f1d07-659">Polly ベースのハンドラーを追加するために使用できる追加の拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="f1d07-660">そのような拡張メソッドの 1 つは `AddPolicyHandler` であり、複数のオーバーロードを持ちます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="f1d07-661">1 つのオーバーロードでは、適用するポリシーを定義するときに要求を検査できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="f1d07-662">上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="f1d07-663">他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="f1d07-664">複数の Polly ハンドラーを追加する</span><span class="sxs-lookup"><span data-stu-id="f1d07-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="f1d07-665">Polly ポリシーを入れ子にして拡張機能を提供するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="f1d07-666">前の例では、2 つのハンドラーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="f1d07-667">最初のハンドラーは、`AddTransientHttpErrorPolicy` 拡張メソッドを使用して再試行ポリシーを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="f1d07-668">失敗した要求は 3 回まで再試行されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="f1d07-669">`AddTransientHttpErrorPolicy` の 2 番目の呼び出しでは、サーキット ブレーカー ポリシーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="f1d07-670">試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="f1d07-671">サーキット ブレーカー ポリシーはステートフルです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="f1d07-672">このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="f1d07-673">Polly レジストリからポリシーを追加する</span><span class="sxs-lookup"><span data-stu-id="f1d07-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="f1d07-674">定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="f1d07-675">提供されている拡張メソッドを使用することで、レジストリからポリシーを使用してハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="f1d07-676">上記のコードでは、`PolicyRegistry` が `ServiceCollection` に追加されるときに 2 つのポリシーが登録されています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="f1d07-677">レジストリからポリシーを使用するには、適用するポリシーの名前を渡して `AddPolicyHandlerFromRegistry` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="f1d07-678">`IHttpClientFactory` および Polly の統合について詳しくは、[Polly の Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="f1d07-679">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="f1d07-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="f1d07-680">`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="f1d07-681">名前付きクライアントごとに <xref:System.Net.Http.HttpMessageHandler> が存在します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="f1d07-682">ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="f1d07-683">`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="f1d07-684">新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="f1d07-685">通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="f1d07-686">必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="f1d07-687">また、一部のハンドラーは接続を無期限に開いており、DNS の変更にハンドラーが対応できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="f1d07-688">ハンドラーの既定の有効期間は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="f1d07-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="f1d07-689">名前付きクライアントごとに、既定値をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="f1d07-690">オーバーライドするには、クライアント作成時に返された `IHttpClientBuilder` で <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="f1d07-691">クライアントを破棄する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f1d07-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="f1d07-692">破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="f1d07-693">`IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="f1d07-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-694">通常、`HttpClient` インスタンスは、破棄を必要としない .NET オブジェクトとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="f1d07-695">`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="f1d07-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="f1d07-696">`IHttpClientFactory` に移行した後は、このパターンは不要になります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="f1d07-697">IHttpClientFactory の代替手段</span><span class="sxs-lookup"><span data-stu-id="f1d07-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="f1d07-698">DI 対応のアプリ内で `IHttpClientFactory` を使用すれば、次のことを回避できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="f1d07-699">`HttpMessageHandler` インスタンスをプールすることによるリソース枯渇の問題。</span><span class="sxs-lookup"><span data-stu-id="f1d07-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="f1d07-700">一定の間隔で `HttpMessageHandler` インスタンスを循環させることによって発生する古くなった DNS の問題。</span><span class="sxs-lookup"><span data-stu-id="f1d07-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="f1d07-701">有効期間の長い <xref:System.Net.Http.SocketsHttpHandler> インスタンスを使用して、上記の問題を解決する別の方法があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="f1d07-702">アプリの起動時に `SocketsHttpHandler` インスタンスを作成し、アプリの有効期間中、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="f1d07-703">DNS の更新時間に基づいて、<xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> を適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="f1d07-704">必要に応じて `new HttpClient(handler, disposeHandler: false)` を使用して `HttpClient` インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="f1d07-705">上記の方法を使用すると、`IHttpClientFactory` が同様の方法で解決するリソース管理の問題を解決できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="f1d07-706">`SocketsHttpHandler` を使用すると、`HttpClient` インスタンス間で接続を共有できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="f1d07-707">この共有によってソケットの枯渇が防止されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="f1d07-708">`SocketsHttpHandler` では、古くなった DNS の問題を回避するために `PooledConnectionLifetime` に従って接続を循環されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="f1d07-709">Cookies</span><span class="sxs-lookup"><span data-stu-id="f1d07-709">Cookies</span></span>

<span data-ttu-id="f1d07-710">`HttpMessageHandler` インスタンスをプールすると、`CookieContainer` オブジェクトが共有されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="f1d07-711">予期せぬ `CookieContainer` オブジェクト共有があると、多くの場合、コードは不適切なものとなります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="f1d07-712">cookie を必要とするアプリの場合は、次のいずれかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="f1d07-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="f1d07-713">自動的な cookie 処理の無効化</span><span class="sxs-lookup"><span data-stu-id="f1d07-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="f1d07-714">`IHttpClientFactory` の回避</span><span class="sxs-lookup"><span data-stu-id="f1d07-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="f1d07-715"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> を呼び出して、自動的な cookie 処理を無効にします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="f1d07-716">ログの記録</span><span class="sxs-lookup"><span data-stu-id="f1d07-716">Logging</span></span>

<span data-ttu-id="f1d07-717">`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="f1d07-718">既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="f1d07-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="f1d07-719">要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="f1d07-720">各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="f1d07-721">たとえば、*MyNamedClient* という名前のクライアントは、カテゴリ `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` でメッセージをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="f1d07-722">*LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="f1d07-723">要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="f1d07-724">応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="f1d07-725">ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="f1d07-726">*MyNamedClient* の例では、それらのメッセージはログ カテゴリ `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` に対して記録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="f1d07-727">要求では、他のすべてのハンドラーが実行した後、要求がネットワークに送信される直前に、これが行われます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="f1d07-728">応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="f1d07-729">パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="f1d07-730">これには、たとえば、要求ヘッダーや応答状態コードへの変更を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="f1d07-731">ログのカテゴリにクライアントの名前を含めると、必要に応じて、特定の名前付きクライアントでログをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="f1d07-732">HttpMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="f1d07-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="f1d07-733">クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="f1d07-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="f1d07-734">名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="f1d07-735"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="f1d07-736">デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="f1d07-737">コンソール アプリで IHttpClientFactory を使用する</span><span class="sxs-lookup"><span data-stu-id="f1d07-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="f1d07-738">コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="f1d07-739">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="f1d07-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="f1d07-740">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="f1d07-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="f1d07-741">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-741">In the following example:</span></span>

* <span data-ttu-id="f1d07-742"><xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="f1d07-743">`MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="f1d07-744">`HttpClient` は Web ページを取得する目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="f1d07-745">`Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="f1d07-746">ヘッダー伝達ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="f1d07-746">Header propagation middleware</span></span>

<span data-ttu-id="f1d07-747">ヘッダー伝達は、受信要求から送信 HTTP クライアント要求に HTTP ヘッダーを伝達するためのコミュニティでサポートされたミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="f1d07-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="f1d07-748">ヘッダー伝達を使用するには、次を行います。</span><span class="sxs-lookup"><span data-stu-id="f1d07-748">To use header propagation:</span></span>

* <span data-ttu-id="f1d07-749">パッケージ [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation) のコミュニティでサポートされているポートを参照します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="f1d07-750">ASP.NET Core 3.1 以降では、[Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f1d07-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="f1d07-751">`Startup` でミドルウェアと `HttpClient` を構成します。</span><span class="sxs-lookup"><span data-stu-id="f1d07-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="f1d07-752">クライアントで、構成されたヘッダーが送信要求に含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1d07-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="f1d07-753">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f1d07-753">Additional resources</span></span>

* [<span data-ttu-id="f1d07-754">HttpClientFactory を使用して回復力の高い HTTP 要求を実装する</span><span class="sxs-lookup"><span data-stu-id="f1d07-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="f1d07-755">HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する</span><span class="sxs-lookup"><span data-stu-id="f1d07-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="f1d07-756">サーキット ブレーカー パターンを実装する</span><span class="sxs-lookup"><span data-stu-id="f1d07-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
