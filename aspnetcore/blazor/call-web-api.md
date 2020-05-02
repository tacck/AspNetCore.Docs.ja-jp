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
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="7a062-103">ASP.NET Core Blazor から Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="7a062-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="7a062-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)、[Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="7a062-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7a062-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) アプリからは、事前に構成された `HttpClient` サービスを使用して Web API を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7a062-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="7a062-106">JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) オプションを含めることができる要求は、Blazor JSON ヘルパーまたは <xref:System.Net.Http.HttpRequestMessage> を使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="7a062-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="7a062-107">Blazor WebAssembly アプリの `HttpClient` サービスは、発行元のサーバーに要求を戻すことに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="7a062-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="7a062-108">このトピックのガイダンスは、Blazor WebAssembly アプリにのみ関連します。</span><span class="sxs-lookup"><span data-stu-id="7a062-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="7a062-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) アプリからは <xref:System.Net.Http.HttpClient> インスタンス (通常、<xref:System.Net.Http.IHttpClientFactory> を使用して作成されます) を使用して Web API を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7a062-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="7a062-110">このトピックのガイダンスは、Blazor Server アプリには関係しません。</span><span class="sxs-lookup"><span data-stu-id="7a062-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="7a062-111">Blazor Server アプリの開発時には、<xref:fundamentals/http-requests> のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="7a062-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="7a062-112">[サンプルコードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([ダウンロードの方法](xref:index#how-to-download-a-sample)) &ndash;*BlazorWebAssemblySample* アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="7a062-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="7a062-113">*BlazorWebAssemblySample* サンプル アプリ内の以下のコンポーネントを参照します。</span><span class="sxs-lookup"><span data-stu-id="7a062-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="7a062-114">Web API の呼び出し (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="7a062-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="7a062-115">HTTP 要求テスター (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="7a062-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="7a062-116">パッケージ</span><span class="sxs-lookup"><span data-stu-id="7a062-116">Packages</span></span>

<span data-ttu-id="7a062-117">プロジェクト ファイルで [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="7a062-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="7a062-118">HttpClient サービスを追加する</span><span class="sxs-lookup"><span data-stu-id="7a062-118">Add the HttpClient service</span></span>

<span data-ttu-id="7a062-119">`Program.Main` で、`HttpClient` サービスがまだ存在しない場合は追加します。</span><span class="sxs-lookup"><span data-stu-id="7a062-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="7a062-120">HttpClient と JSON ヘルパー</span><span class="sxs-lookup"><span data-stu-id="7a062-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="7a062-121">Blazor WebAssembly アプリでは、要求を要求元のサーバーに返すための構成済みのサービスとして [HttpClient](xref:fundamentals/http-requests) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="7a062-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="7a062-122">Blazor Server アプリには、既定では `HttpClient` サービスが含まれません。</span><span class="sxs-lookup"><span data-stu-id="7a062-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="7a062-123">[HttpClient ファクトリ インフラストラクチャ](xref:fundamentals/http-requests)を使用して、アプリに `HttpClient` を提供します。</span><span class="sxs-lookup"><span data-stu-id="7a062-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="7a062-124">`HttpClient` と JSON のヘルパーは、サード パーティの Web API エンドポイントを呼び出すためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="7a062-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="7a062-125">`HttpClient` は、ブラウザーの [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) を使用して実装されており、同一生成元ポリシーの適用を含め、その制限に従います。</span><span class="sxs-lookup"><span data-stu-id="7a062-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="7a062-126">クライアントのベース アドレスは、生成元のサーバーのアドレスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="7a062-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="7a062-127">`HttpClient` インスタンスは `@inject` ディレクティブを使用して挿入します。</span><span class="sxs-lookup"><span data-stu-id="7a062-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="7a062-128">以降の例では、Todo Web API によって、(CRUD) 操作の作成、読み取り、更新、および削除が処理されます。</span><span class="sxs-lookup"><span data-stu-id="7a062-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="7a062-129">これらの例は、以下を格納する `TodoItem` クラスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="7a062-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="7a062-130">ID (`Id`、`long`) &ndash; 項目の一意の ID。</span><span class="sxs-lookup"><span data-stu-id="7a062-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="7a062-131">名前 (`Name`、`string`) &ndash; 項目の名前。</span><span class="sxs-lookup"><span data-stu-id="7a062-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="7a062-132">状態 (`IsComplete`、`bool`) &ndash; Todo 項目が終了したかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="7a062-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="7a062-133">JSON のヘルパー メソッドは、要求を URI (次の例では Web API) に送信し、応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="7a062-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="7a062-134">`GetFromJsonAsync` &ndash; HTTP GET 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="7a062-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7a062-135">次のコードでは、コンポーネントによって `_todoItems` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7a062-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="7a062-136">`GetTodoItems` メソッドは、コンポーネントのレンダリングが終了したとき ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)) にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="7a062-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="7a062-137">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a062-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="7a062-138">`PostAsJsonAsync` &ndash; JSON でエンコードされたコンテンツを含め、HTTP POST 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="7a062-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7a062-139">次のコードでは、コンポーネントのバインドされた要素によって `_newItemName` が提供されています。</span><span class="sxs-lookup"><span data-stu-id="7a062-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="7a062-140">`AddItem` メソッドは、`<button>` 要素を選択することにでトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="7a062-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="7a062-141">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a062-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="7a062-142">`PostAsJsonAsync` を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。</span><span class="sxs-lookup"><span data-stu-id="7a062-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="7a062-143">応答メッセージから JSON コンテンツを逆シリアル化するには、`ReadFromJsonAsync<T>` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7a062-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="7a062-144">`PutAsJsonAsync` &ndash; JSON でエンコードされたコンテンツを含め、HTTP PUT 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="7a062-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="7a062-145">次のコードでは、`Name` および `IsCompleted` の `_editItem` 値が、コンポーネントのバインドされた要素によって提供されています。</span><span class="sxs-lookup"><span data-stu-id="7a062-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="7a062-146">項目の `Id` は、UI の別の部分で項目が選択され、`EditItem` が呼び出されたときに設定されます。</span><span class="sxs-lookup"><span data-stu-id="7a062-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="7a062-147">`SaveItem` メソッドは、Save `<button>` 要素を選択することでトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="7a062-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="7a062-148">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a062-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="7a062-149">`PutAsJsonAsync` を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。</span><span class="sxs-lookup"><span data-stu-id="7a062-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="7a062-150">応答メッセージから JSON コンテンツを逆シリアル化するには、`ReadFromJsonAsync<T>` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7a062-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="7a062-151"><xref:System.Net.Http> には、HTTP 要求を送信し、HTTP 応答を受信するための追加の拡張メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7a062-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="7a062-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) は、HTTP DELETE 要求を Web API に送信するために使用します。</span><span class="sxs-lookup"><span data-stu-id="7a062-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="7a062-153">次のコードでは、Delete `<button>` 要素で `DeleteItem` メソッドを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="7a062-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="7a062-154">バインドされた `<input>` 要素では、削除する項目の `id` が提供されます。</span><span class="sxs-lookup"><span data-stu-id="7a062-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="7a062-155">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a062-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="7a062-156">クロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="7a062-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="7a062-157">Web ページでは、ブラウザーのセキュリティにより、その Web ページを提供したドメインと異なるドメインに対して要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="7a062-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="7a062-158">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="7a062-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="7a062-159">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="7a062-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="7a062-160">オリジンが異なるエンドポイントへの要求をブラウザーから行うには、*エンドポイント*で[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a062-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="7a062-161">[Blazor WebAssembly サンプル アプリ (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) は、呼び出し Web API コンポーネント (*Pages/CallWebAPI.razor*) で CORS を使用する具体的な方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7a062-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="7a062-162">他のサイトがアプリに対してクロス オリジン リソース共有 (CORS) 要求を行えるようにするには、「<xref:security/cors>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a062-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="7a062-163">Fetch API 要求オプションを使用する HttpClient と HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="7a062-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="7a062-164">Blazor WebAssembly アプリで WebAssembly を実行するときには、[HttpClient](xref:fundamentals/http-requests) および <xref:System.Net.Http.HttpRequestMessage> を使用して要求をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="7a062-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="7a062-165">たとえば、要求 URI、HTTP メソッド、および必要なすべての要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="7a062-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="7a062-166">.NET WebAssembly の `HttpClient` の実装には、[WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="7a062-166">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="7a062-167">フェッチを使用すると、いくつかの[要求固有のオプション](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="7a062-167">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="7a062-168">HTTP フェッチ要求オプションは、次の表に示す `HttpRequestMessage` 拡張メソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="7a062-168">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="7a062-169">`HttpRequestMessage` 拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="7a062-169">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="7a062-170">フェッチ要求プロパティ</span><span class="sxs-lookup"><span data-stu-id="7a062-170">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="7a062-171">credentials</span><span class="sxs-lookup"><span data-stu-id="7a062-171">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="7a062-172">cache</span><span class="sxs-lookup"><span data-stu-id="7a062-172">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="7a062-173">モード</span><span class="sxs-lookup"><span data-stu-id="7a062-173">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="7a062-174">integrity</span><span class="sxs-lookup"><span data-stu-id="7a062-174">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="7a062-175">より汎用的な `SetBrowserRequestOption` 拡張メソッドを使用してその他のオプションを設定できます。</span><span class="sxs-lookup"><span data-stu-id="7a062-175">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="7a062-176">通常、HTTP 応答は、応答コンテンツに対する同期読み取りのサポートを有効にするために Blazor WebAssembly アプリでバッファリングされます。</span><span class="sxs-lookup"><span data-stu-id="7a062-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="7a062-177">応答ストリーミングのサポートを有効にするには、要求に対して `SetBrowserResponseStreamingEnabled` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7a062-177">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="7a062-178">クロスオリジン要求に資格情報を含めるには、`SetBrowserRequestCredentials` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7a062-178">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="7a062-179">Fetch API オプションの詳細については、[MDN の Web ドキュメント、WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a062-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="7a062-180">CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信するときには、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a062-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="7a062-181">次のポリシーには、以下についての構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7a062-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="7a062-182">要求元 (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="7a062-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="7a062-183">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="7a062-183">Any method (verb).</span></span>
* <span data-ttu-id="7a062-184">`Content-Type` ヘッダーと `Authorization` ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="7a062-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="7a062-185">カスタム ヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> の呼び出し時にヘッダーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="7a062-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="7a062-186">クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include`に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="7a062-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="7a062-187">詳細については、「<xref:security/cors>」と、サンプル アプリの HTTP 要求テスター コンポーネント (*Components/HTTPRequestTester.razor*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a062-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a062-188">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7a062-188">Additional resources</span></span>

* [<span data-ttu-id="7a062-189">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="7a062-189">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7a062-190">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="7a062-190">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="7a062-191">Kestrel HTTPS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="7a062-191">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="7a062-192">W3C におけるクロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="7a062-192">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
