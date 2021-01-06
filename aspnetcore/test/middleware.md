---
title: ASP.NET Core のミドルウェアのテスト
author: tratcher
description: TestServer を使用して ASP.NET Core のミドルウェアをテストする方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
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
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93051435"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="e38ca-103">ASP.NET Core のミドルウェアのテスト</span><span class="sxs-lookup"><span data-stu-id="e38ca-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="e38ca-104">作成者: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e38ca-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="e38ca-105">ミドルウェアは、<xref:Microsoft.AspNetCore.TestHost.TestServer> と切り離してテストできます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="e38ca-106">これにより次の操作を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-106">It allows you to:</span></span>

* <span data-ttu-id="e38ca-107">テストするコンポーネントのみを含むアプリ パイプラインをインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="e38ca-108">カスタム要求を送信してミドルウェアの動作を検証します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="e38ca-109">利点:</span><span class="sxs-lookup"><span data-stu-id="e38ca-109">Advantages:</span></span>

* <span data-ttu-id="e38ca-110">要求はネットワークを介してシリアル化されるのではなく、メモリ内で送信されます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="e38ca-111">これにより、ポート管理や HTTPS 証明書などの追加の問題が回避されます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="e38ca-112">ミドルウェア内の例外は、呼び出し元のテスト フローに直接戻すことができます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="e38ca-113"><xref:Microsoft.AspNetCore.Http.HttpContext> などのサーバーのデータ構造をテストで直接カスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="e38ca-114">TestServer の設定</span><span class="sxs-lookup"><span data-stu-id="e38ca-114">Set up the TestServer</span></span>

<span data-ttu-id="e38ca-115">テスト プロジェクトで、テストを作成します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-115">In the test project, create a test:</span></span>

* <span data-ttu-id="e38ca-116"><xref:Microsoft.AspNetCore.TestHost.TestServer> を使用するホストをビルドして起動します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="e38ca-117">ミドルウェアが使用する必要なサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="e38ca-118">[Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="e38ca-119">テストにミドルウェアを使用するように処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="e38ca-120">HttpClient での要求の送信</span><span class="sxs-lookup"><span data-stu-id="e38ca-120">Send requests with HttpClient</span></span>

<span data-ttu-id="e38ca-121"><xref:System.Net.Http.HttpClient> を使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="e38ca-122">結果をアサートします。</span><span class="sxs-lookup"><span data-stu-id="e38ca-122">Assert the result.</span></span> <span data-ttu-id="e38ca-123">まず、予想した結果とは逆のアサーションを行います。</span><span class="sxs-lookup"><span data-stu-id="e38ca-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="e38ca-124">偽陽性のアサーションを使用した最初の実行では、ミドルウェアが正常に実行されているときにテストが失敗することを確認します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="e38ca-125">テストを実行し、テストが失敗することを確認します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="e38ca-126">次の例では、ルート エンドポイントが要求されたときに、ミドルウェアによって状態コード 404 (*見つかりません*) が返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="e38ca-126">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="e38ca-127">最初のテストを `Assert.NotEqual( ... );` で実行します。これは失敗します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="e38ca-128">通常の動作状態でミドルウェアをテストするようにアサーションを変更します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="e38ca-129">最終テストでは、`Assert.Equal( ... );` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="e38ca-130">もう一度テストを実行して、成功することを確認します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="e38ca-131">HttpContext での要求の送信</span><span class="sxs-lookup"><span data-stu-id="e38ca-131">Send requests with HttpContext</span></span>

<span data-ttu-id="e38ca-132">テスト アプリでは、[SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) を使用して要求を送信することもできます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="e38ca-133">次の例では、ミドルウェアによって `https://example.com/A/Path/?and=query` が処理されるときに、いくつかのチェックが行われます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="e38ca-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> は、<xref:System.Net.Http.HttpClient> の抽象化を使用するのではなく、<xref:Microsoft.AspNetCore.Http.HttpContext> オブジェクトの直接構成を許可します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="e38ca-135"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> を使用すると、[HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) や [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) など、サーバー上でのみ使用可能な構造体を操作できます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="e38ca-136">"*404 - 見つかりません*" 応答をテストした前の例と同じように、前のテストの各 `Assert` ステートメントの逆をチェックします。</span><span class="sxs-lookup"><span data-stu-id="e38ca-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="e38ca-137">このチェックによって、ミドルウェアが正常に動作しているときにテストが正しく失敗しないことが確認されます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="e38ca-138">偽陽性のテストが動作することを確認したら、期待されるテストの条件と値に最終的な `Assert` ステートメントを設定します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="e38ca-139">もう一度実行して、テストが成功することを確認します。</span><span class="sxs-lookup"><span data-stu-id="e38ca-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="e38ca-140">TestServer の制限事項</span><span class="sxs-lookup"><span data-stu-id="e38ca-140">TestServer limitations</span></span>

<span data-ttu-id="e38ca-141">TestServer:</span><span class="sxs-lookup"><span data-stu-id="e38ca-141">TestServer:</span></span>

* <span data-ttu-id="e38ca-142">サーバーの動作をレプリケートしてミドルウェアをテストするように作成されています。</span><span class="sxs-lookup"><span data-stu-id="e38ca-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="e38ca-143"><xref:System.Net.Http.HttpClient> のすべての動作をレプリケートしようとは "\***しません** _" 。</span><span class="sxs-lookup"><span data-stu-id="e38ca-143">Does \***not** _ try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
<span data-ttu-id="e38ca-144">_ クライアントに可能な限りサーバーを制御できるアクセス権を付与し、サーバーで起こっていることの可視性をできるだけ高めるようにします。</span><span class="sxs-lookup"><span data-stu-id="e38ca-144">_ Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="e38ca-145">たとえば、サーバー状態を直接通信するために、`HttpClient` によって通常スローされない例外がスローされる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e38ca-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="e38ca-146">通常、ミドルウェアに関連しないため、既定ではトランスポート固有のヘッダーの一部は設定しません。</span><span class="sxs-lookup"><span data-stu-id="e38ca-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="e38ca-147">詳細については、次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e38ca-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="e38ca-148">Content-Length ヘッダーと Transfer-Encoding ヘッダー</span><span class="sxs-lookup"><span data-stu-id="e38ca-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="e38ca-149">TestServer では、[Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) や [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding) などのトランスポート関連の要求ヘッダーまたは応答ヘッダーが設定 "\***されません** _"。</span><span class="sxs-lookup"><span data-stu-id="e38ca-149">TestServer does \***not** _ set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="e38ca-150">アプリケーションの使用方法はクライアント、シナリオ、プロトコルによって異なるため、これらのヘッダーに依存しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e38ca-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="e38ca-151">特定のシナリオをテストするために `Content-Length` および `Transfer-Encoding` が必要な場合は、<xref:System.Net.Http.HttpRequestMessage> または <xref:Microsoft.AspNetCore.Http.HttpContext> を作成するときにテストで指定することができます。</span><span class="sxs-lookup"><span data-stu-id="e38ca-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="e38ca-152">詳しくは、次の GitHub の問題をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e38ca-152">For more information, see the following GitHub issues:</span></span>

<span data-ttu-id="e38ca-153">_ [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span><span class="sxs-lookup"><span data-stu-id="e38ca-153">_ [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span></span>
* [<span data-ttu-id="e38ca-154">dotnet/aspnetcore#18463</span><span class="sxs-lookup"><span data-stu-id="e38ca-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="e38ca-155">dotnet/aspnetcore#13273</span><span class="sxs-lookup"><span data-stu-id="e38ca-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
