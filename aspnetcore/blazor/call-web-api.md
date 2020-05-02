---
title: ASP.NET Core Blazor WebAssembly から Web API を呼び出す
author: guardrex
description: クロス オリジン リソース共有 (CORS) 要求の作成など、JSON ヘルパーを使用して Blazor WebAssembly アプリから Web API を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122219"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>ASP.NET Core Blazor から Web API を呼び出す

作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)、[Juan De la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) アプリからは、事前に構成された `HttpClient` サービスを使用して Web API を呼び出します。 JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) オプションを含めることができる要求は、Blazor JSON ヘルパーまたは <xref:System.Net.Http.HttpRequestMessage> を使用して作成します。 Blazor WebAssembly アプリの `HttpClient` サービスは、発行元のサーバーに要求を戻すことに重点を置いています。 このトピックのガイダンスは、Blazor WebAssembly アプリにのみ関連します。

[Blazor Server](xref:blazor/hosting-models#blazor-server) アプリからは <xref:System.Net.Http.HttpClient> インスタンス (通常、<xref:System.Net.Http.IHttpClientFactory> を使用して作成されます) を使用して Web API を呼び出します。 このトピックのガイダンスは、Blazor Server アプリには関係しません。 Blazor Server アプリの開発時には、<xref:fundamentals/http-requests> のガイダンスに従ってください。

[サンプルコードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([ダウンロードの方法](xref:index#how-to-download-a-sample)) &ndash;*BlazorWebAssemblySample* アプリを選択します。

*BlazorWebAssemblySample* サンプル アプリ内の以下のコンポーネントを参照します。

* Web API の呼び出し (*Pages/CallWebAPI.razor*)
* HTTP 要求テスター (*Components/HTTPRequestTester.razor*)

## <a name="packages"></a>パッケージ

プロジェクト ファイルで [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet パッケージを参照します。

## <a name="add-the-httpclient-service"></a>HttpClient サービスを追加する

`Program.Main` で、`HttpClient` サービスがまだ存在しない場合は追加します。

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient と JSON ヘルパー

Blazor WebAssembly アプリでは、要求を要求元のサーバーに返すための構成済みのサービスとして [HttpClient](xref:fundamentals/http-requests) を使用できます。

Blazor Server アプリには、既定では `HttpClient` サービスが含まれません。 [HttpClient ファクトリ インフラストラクチャ](xref:fundamentals/http-requests)を使用して、アプリに `HttpClient` を提供します。

`HttpClient` と JSON のヘルパーは、サード パーティの Web API エンドポイントを呼び出すためにも使用されます。 `HttpClient` は、ブラウザーの [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) を使用して実装されており、同一生成元ポリシーの適用を含め、その制限に従います。

クライアントのベース アドレスは、生成元のサーバーのアドレスに設定されます。 `HttpClient` インスタンスは `@inject` ディレクティブを使用して挿入します。

```razor
@using System.Net.Http
@inject HttpClient Http
```

以降の例では、Todo Web API によって、(CRUD) 操作の作成、読み取り、更新、および削除が処理されます。 これらの例は、以下を格納する `TodoItem` クラスに基づいています。

* ID (`Id`、`long`) &ndash; 項目の一意の ID。
* 名前 (`Name`、`string`) &ndash; 項目の名前。
* 状態 (`IsComplete`、`bool`) &ndash; Todo 項目が終了したかどうかを示します。

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON のヘルパー メソッドは、要求を URI (次の例では Web API) に送信し、応答を処理します。

* `GetFromJsonAsync` &ndash; HTTP GET 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。

  次のコードでは、コンポーネントによって `_todoItems` が表示されます。 `GetTodoItems` メソッドは、コンポーネントのレンダリングが終了したとき ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)) にトリガーされます。 完全な例についてはサンプル アプリを参照してください。

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync` &ndash; JSON でエンコードされたコンテンツを含め、HTTP POST 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。

  次のコードでは、コンポーネントのバインドされた要素によって `_newItemName` が提供されています。 `AddItem` メソッドは、`<button>` 要素を選択することにでトリガーされます。 完全な例についてはサンプル アプリを参照してください。

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  `PostAsJsonAsync` を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。 応答メッセージから JSON コンテンツを逆シリアル化するには、`ReadFromJsonAsync<T>` 拡張メソッドを使用します。
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync` &ndash; JSON でエンコードされたコンテンツを含め、HTTP PUT 要求を送信します。

  次のコードでは、`Name` および `IsCompleted` の `_editItem` 値が、コンポーネントのバインドされた要素によって提供されています。 項目の `Id` は、UI の別の部分で項目が選択され、`EditItem` が呼び出されたときに設定されます。 `SaveItem` メソッドは、Save `<button>` 要素を選択することでトリガーされます。 完全な例についてはサンプル アプリを参照してください。

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  `PutAsJsonAsync` を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。 応答メッセージから JSON コンテンツを逆シリアル化するには、`ReadFromJsonAsync<T>` 拡張メソッドを使用します。
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> には、HTTP 要求を送信し、HTTP 応答を受信するための追加の拡張メソッドが含まれています。 [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) は、HTTP DELETE 要求を Web API に送信するために使用します。

次のコードでは、Delete `<button>` 要素で `DeleteItem` メソッドを呼び出しています。 バインドされた `<input>` 要素では、削除する項目の `id` が提供されます。 完全な例についてはサンプル アプリを参照してください。

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS)

Web ページでは、ブラウザーのセキュリティにより、その Web ページを提供したドメインと異なるドメインに対して要求を行うことはできません。 この制限は、*同一オリジン ポリシー*と呼ばれます。 同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。 オリジンが異なるエンドポイントへの要求をブラウザーから行うには、*エンドポイント*で[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) を有効にする必要があります。

[Blazor WebAssembly サンプル アプリ (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) は、呼び出し Web API コンポーネント (*Pages/CallWebAPI.razor*) で CORS を使用する具体的な方法を示しています。

他のサイトがアプリに対してクロス オリジン リソース共有 (CORS) 要求を行えるようにするには、「<xref:security/cors>」を参照してください。

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>Fetch API 要求オプションを使用する HttpClient と HttpRequestMessage

Blazor WebAssembly アプリで WebAssembly を実行するときには、[HttpClient](xref:fundamentals/http-requests) および <xref:System.Net.Http.HttpRequestMessage> を使用して要求をカスタマイズします。 たとえば、要求 URI、HTTP メソッド、および必要なすべての要求ヘッダーを指定できます。

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

.NET WebAssembly の `HttpClient` の実装には、[WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch) が使用されます。 フェッチを使用すると、いくつかの[要求固有のオプション](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を構成できます。 

HTTP フェッチ要求オプションは、次の表に示す `HttpRequestMessage` 拡張メソッドを使用して構成できます。

| `HttpRequestMessage` 拡張メソッド | フェッチ要求プロパティ |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [モード](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

より汎用的な `SetBrowserRequestOption` 拡張メソッドを使用してその他のオプションを設定できます。
 
通常、HTTP 応答は、応答コンテンツに対する同期読み取りのサポートを有効にするために Blazor WebAssembly アプリでバッファリングされます。 応答ストリーミングのサポートを有効にするには、要求に対して `SetBrowserResponseStreamingEnabled` 拡張メソッドを使用します。

クロスオリジン要求に資格情報を含めるには、`SetBrowserRequestCredentials` 拡張メソッドを使用します。

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Fetch API オプションの詳細については、[MDN の Web ドキュメント、WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) を参照してください。

CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信するときには、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。

次のポリシーには、以下についての構成が含まれています。

* 要求元 (`http://localhost:5000`、`https://localhost:5001`)。
* 任意のメソッド (動詞)。
* `Content-Type` ヘッダーと `Authorization` ヘッダー。 カスタム ヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> の呼び出し時にヘッダーを一覧表示します。
* クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include`に設定されています)。

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

詳細については、「<xref:security/cors>」と、サンプル アプリの HTTP 要求テスター コンポーネント (*Components/HTTPRequestTester.razor*) を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* [追加のアクセス トークンを要求する](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [送信要求にトークンを添付する](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kestrel HTTPS エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [W3C におけるクロスオリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/)
