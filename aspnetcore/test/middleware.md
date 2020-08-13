---
title: ASP.NET Core のミドルウェアのテスト
author: tratcher
description: TestServer を使用して ASP.NET Core のミドルウェアをテストする方法について学習します。
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
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
uid: test/middleware
ms.openlocfilehash: 235010c95cdd0c7ce1368b4abd91e75d81ae094b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021901"
---
# <a name="test-aspnet-core-middleware"></a>ASP.NET Core のミドルウェアのテスト

作成者: [Chris Ross](https://github.com/Tratcher)

ミドルウェアは、<xref:Microsoft.AspNetCore.TestHost.TestServer> と切り離してテストできます。 これにより次の操作を行うことができます。

* テストするコンポーネントのみを含むアプリ パイプラインをインスタンス化します。
* カスタム要求を送信してミドルウェアの動作を検証します。

利点:

* 要求はネットワークを介してシリアル化されるのではなく、メモリ内で送信されます。
* これにより、ポート管理や HTTPS 証明書などの追加の問題が回避されます。
* ミドルウェア内の例外は、呼び出し元のテスト フローに直接戻すことができます。
* <xref:Microsoft.AspNetCore.Http.HttpContext> などのサーバーのデータ構造をテストで直接カスタマイズすることができます。

## <a name="set-up-the-testserver"></a>TestServer の設定

テスト プロジェクトで、テストを作成します。

* <xref:Microsoft.AspNetCore.TestHost.TestServer> を使用するホストをビルドして起動します。
* ミドルウェアが使用する必要なサービスを追加します。
* [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet パッケージをプロジェクトに追加します。
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* テストにミドルウェアを使用するように処理パイプラインを構成します。

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>HttpClient での要求の送信
<xref:System.Net.Http.HttpClient> を使用して要求を送信します。

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

結果をアサートします。 まず、予想した結果とは逆のアサーションを行います。 偽陽性のアサーションを使用した最初の実行では、ミドルウェアが正常に実行されているときにテストが失敗することを確認します。 テストを実行し、テストが失敗することを確認します。

次の例では、ルート エンドポイントが要求されたときに、ミドルウェアによって状態コード 404 (*見つかりません*) が返される必要があります。 最初のテストを `Assert.NotEqual( ... );` で実行します。これは失敗します。

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

通常の動作状態でミドルウェアをテストするようにアサーションを変更します。 最終テストでは、`Assert.Equal( ... );` を使用します。 もう一度テストを実行して、成功することを確認します。

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>HttpContext での要求の送信

テスト アプリでは、[SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) を使用して要求を送信することもできます。 次の例では、ミドルウェアによって `https://example.com/A/Path/?and=query` が処理されるときに、いくつかのチェックが行われます。

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> は、<xref:System.Net.Http.HttpClient> の抽象化を使用するのではなく、<xref:Microsoft.AspNetCore.Http.HttpContext> オブジェクトの直接構成を許可します。 <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> を使用すると、[HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) や [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) など、サーバー上でのみ使用可能な構造体を操作できます。

"*404 - 見つかりません*" 応答をテストした前の例と同じように、前のテストの各 `Assert` ステートメントの逆をチェックします。 このチェックによって、ミドルウェアが正常に動作しているときにテストが正しく失敗しないことが確認されます。 偽陽性のテストが動作することを確認したら、期待されるテストの条件と値に最終的な `Assert` ステートメントを設定します。 もう一度実行して、テストが成功することを確認します。

## <a name="testserver-limitations"></a>TestServer の制限事項

TestServer:

* サーバーの動作をレプリケートしてミドルウェアをテストするように作成されています。
* すべての <xref:System.Net.Http.HttpClient> 動作をレプリケートしようとは***しません***。
* クライアントに可能な限りサーバーを制御できるアクセス権を付与し、サーバーで起こっていることの可視性をできるだけ高めるようにします。 たとえば、サーバー状態を直接通信するために、`HttpClient` によって通常スローされない例外がスローされる場合があります。
* 通常、ミドルウェアに関連しないため、既定ではトランスポート固有のヘッダーの一部は設定しません。 詳細については、次のセクションを参照してください。

### <a name="content-length-and-transfer-encoding-headers"></a>Content-Length ヘッダーと Transfer-Encoding ヘッダー

TestServer では、[Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) や [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding) などのトランスポート関連の要求ヘッダーまたは応答ヘッダーを設定***しません***。 アプリケーションの使用方法はクライアント、シナリオ、プロトコルによって異なるため、これらのヘッダーに依存しないようにする必要があります。 特定のシナリオをテストするために `Content-Length` および `Transfer-Encoding` が必要な場合は、<xref:System.Net.Http.HttpRequestMessage> または <xref:Microsoft.AspNetCore.Http.HttpContext> を作成するときにテストで指定することができます。 詳しくは、次の GitHub の問題をご覧ください。

* [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [dotnet/aspnetcore#18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [dotnet/aspnetcore#13273](https://github.com/dotnet/aspnetcore/issues/13273)
