---
title: ASP.NET Core のミドルウェアのテスト
author: tratcher
description: TestServer を使用して ASP.NET Core のミドルウェアをテストする方法について学習します。
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876423"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="434c2-103">ASP.NET Core のミドルウェアのテスト</span><span class="sxs-lookup"><span data-stu-id="434c2-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="434c2-104">作成者: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="434c2-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="434c2-105">ミドルウェアは、<xref:Microsoft.AspNetCore.TestHost.TestServer> と切り離してテストできます。</span><span class="sxs-lookup"><span data-stu-id="434c2-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="434c2-106">これにより次の操作を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="434c2-106">It allows you to:</span></span>

* <span data-ttu-id="434c2-107">テストするコンポーネントのみを含むアプリ パイプラインをインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="434c2-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="434c2-108">カスタム要求を送信してミドルウェアの動作を検証します。</span><span class="sxs-lookup"><span data-stu-id="434c2-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="434c2-109">利点:</span><span class="sxs-lookup"><span data-stu-id="434c2-109">Advantages:</span></span>

* <span data-ttu-id="434c2-110">要求はネットワークを介してシリアル化されるのではなく、メモリ内で送信されます。</span><span class="sxs-lookup"><span data-stu-id="434c2-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="434c2-111">これにより、ポート管理や HTTPS 証明書などの追加の問題が回避されます。</span><span class="sxs-lookup"><span data-stu-id="434c2-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="434c2-112">ミドルウェア内の例外は、呼び出し元のテスト フローに直接戻すことができます。</span><span class="sxs-lookup"><span data-stu-id="434c2-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="434c2-113"><xref:Microsoft.AspNetCore.Http.HttpContext> などのサーバーのデータ構造をテストで直接カスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="434c2-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="434c2-114">TestServer の設定</span><span class="sxs-lookup"><span data-stu-id="434c2-114">Set up the TestServer</span></span>

<span data-ttu-id="434c2-115">テスト プロジェクトで、テストを作成します。</span><span class="sxs-lookup"><span data-stu-id="434c2-115">In the test project, create a test:</span></span>

* <span data-ttu-id="434c2-116"><xref:Microsoft.AspNetCore.TestHost.TestServer> を使用するホストをビルドして起動します。</span><span class="sxs-lookup"><span data-stu-id="434c2-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="434c2-117">ミドルウェアが使用する必要なサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="434c2-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="434c2-118">テストにミドルウェアを使用するように処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="434c2-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="434c2-119">HttpClient での要求の送信</span><span class="sxs-lookup"><span data-stu-id="434c2-119">Send requests with HttpClient</span></span>
<span data-ttu-id="434c2-120"><xref:System.Net.Http.HttpClient> を使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="434c2-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="434c2-121">結果をアサートします。</span><span class="sxs-lookup"><span data-stu-id="434c2-121">Assert the result.</span></span> <span data-ttu-id="434c2-122">まず、予想した結果とは逆のアサーションを行います。</span><span class="sxs-lookup"><span data-stu-id="434c2-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="434c2-123">偽陽性のアサーションを使用した最初の実行では、ミドルウェアが正常に実行されているときにテストが失敗することを確認します。</span><span class="sxs-lookup"><span data-stu-id="434c2-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="434c2-124">テストを実行し、テストが失敗することを確認します。</span><span class="sxs-lookup"><span data-stu-id="434c2-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="434c2-125">次の例では、ルート エンドポイントが要求されたときに、ミドルウェアによって状態コード 404 (*見つかりません*) が返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="434c2-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="434c2-126">最初のテストを `Assert.NotEqual( ... );` で実行します。これは失敗します。</span><span class="sxs-lookup"><span data-stu-id="434c2-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="434c2-127">通常の動作状態でミドルウェアをテストするようにアサーションを変更します。</span><span class="sxs-lookup"><span data-stu-id="434c2-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="434c2-128">最終テストでは、`Assert.Equal( ... );` を使用します。</span><span class="sxs-lookup"><span data-stu-id="434c2-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="434c2-129">もう一度テストを実行して、成功することを確認します。</span><span class="sxs-lookup"><span data-stu-id="434c2-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="434c2-130">HttpContext での要求の送信</span><span class="sxs-lookup"><span data-stu-id="434c2-130">Send requests with HttpContext</span></span>

<span data-ttu-id="434c2-131">テスト アプリでは、[SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) を使用して要求を送信することもできます。</span><span class="sxs-lookup"><span data-stu-id="434c2-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="434c2-132">次の例では、ミドルウェアによって `https://example.com/A/Path/?and=query` が処理されるときに、いくつかのチェックが行われます。</span><span class="sxs-lookup"><span data-stu-id="434c2-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="434c2-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> は、<xref:System.Net.Http.HttpClient> の抽象化を使用するのではなく、<xref:Microsoft.AspNetCore.Http.HttpContext> オブジェクトの直接構成を許可します。</span><span class="sxs-lookup"><span data-stu-id="434c2-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="434c2-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> を使用すると、[HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) や [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) など、サーバー上でのみ使用可能な構造体を操作できます。</span><span class="sxs-lookup"><span data-stu-id="434c2-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="434c2-135">"*404 - 見つかりません*" 応答をテストした前の例と同じように、前のテストの各 `Assert` ステートメントの逆をチェックします。</span><span class="sxs-lookup"><span data-stu-id="434c2-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="434c2-136">このチェックによって、ミドルウェアが正常に動作しているときにテストが正しく失敗しないことが確認されます。</span><span class="sxs-lookup"><span data-stu-id="434c2-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="434c2-137">偽陽性のテストが動作することを確認したら、期待されるテストの条件と値に最終的な `Assert` ステートメントを設定します。</span><span class="sxs-lookup"><span data-stu-id="434c2-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="434c2-138">もう一度実行して、テストが成功することを確認します。</span><span class="sxs-lookup"><span data-stu-id="434c2-138">Run it again to confirm that the test passes.</span></span>
