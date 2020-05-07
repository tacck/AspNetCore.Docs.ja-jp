---
title: ASP.NET Core Blazor WebAssembly から Web API を呼び出す
author: guardrex
description: クロス オリジン リソース共有 (CORS) 要求の作成など、JSON ヘルパーを使用して Blazor WebAssembly アプリから Web API を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767149"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="80a61-103">ASP.NET Core Blazor から Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="80a61-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="80a61-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)、[Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="80a61-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="80a61-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) アプリは、事前に構成された `HttpClient` サービスを使用して Web API を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="80a61-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="80a61-106">JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) オプションを含めることができる要求は、Blazor JSON ヘルパーまたは <xref:System.Net.Http.HttpRequestMessage> を使用して作成ます。</span><span class="sxs-lookup"><span data-stu-id="80a61-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="80a61-107">Blazor WebAssembly アプリの `HttpClient` サービスは、発行元のサーバーに要求を戻すことに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="80a61-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="80a61-108">このトピックのガイダンスは、Blazor WebAssembly アプリにのみ関連します。</span><span class="sxs-lookup"><span data-stu-id="80a61-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="80a61-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) アプリは、<xref:System.Net.Http.HttpClient> インスタンスを使用して Web API を呼び出します。これは通常、<xref:System.Net.Http.IHttpClientFactory> を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="80a61-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="80a61-110">このトピックのガイダンスは、Blazor Server アプリには関係しません。</span><span class="sxs-lookup"><span data-stu-id="80a61-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="80a61-111">Blazor Server アプリの開発時には、<xref:fundamentals/http-requests> のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="80a61-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="80a61-112">[サンプルコードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([ダウンロードの方法](xref:index#how-to-download-a-sample)) &ndash;*BlazorWebAssemblySample* アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="80a61-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="80a61-113">*BlazorWebAssemblySample* サンプル アプリ内の以下のコンポーネントを参照します。</span><span class="sxs-lookup"><span data-stu-id="80a61-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="80a61-114">Web API の呼び出し (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="80a61-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="80a61-115">HTTP 要求テスター (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="80a61-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="80a61-116">パッケージ</span><span class="sxs-lookup"><span data-stu-id="80a61-116">Packages</span></span>

<span data-ttu-id="80a61-117">プロジェクト ファイルで [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="80a61-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="80a61-118">HttpClient サービスを追加する</span><span class="sxs-lookup"><span data-stu-id="80a61-118">Add the HttpClient service</span></span>

<span data-ttu-id="80a61-119">`Program.Main` で、`HttpClient` サービスがまだ存在しない場合は追加します。</span><span class="sxs-lookup"><span data-stu-id="80a61-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="80a61-120">HttpClient と JSON ヘルパー</span><span class="sxs-lookup"><span data-stu-id="80a61-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="80a61-121">Blazor WebAssembly アプリでは、[HttpClient](xref:fundamentals/http-requests) は、要求を要求元のサーバーに返すための構成済みのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="80a61-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="80a61-122">Blazor Server アプリには、既定では `HttpClient` サービスが含まれません。</span><span class="sxs-lookup"><span data-stu-id="80a61-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="80a61-123">[HttpClient ファクトリ インフラストラクチャ](xref:fundamentals/http-requests)を使用して、アプリに `HttpClient` を提供します。</span><span class="sxs-lookup"><span data-stu-id="80a61-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="80a61-124">`HttpClient` と JSON のヘルパーは、サード パーティの Web API エンドポイントを呼び出すためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="80a61-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="80a61-125">`HttpClient` は、ブラウザーの [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) を使用して実装されており、同一生成元ポリシーの適用を含め、その制限に従います。</span><span class="sxs-lookup"><span data-stu-id="80a61-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="80a61-126">クライアントのベース アドレスは、生成元のサーバーのアドレスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="80a61-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="80a61-127">`HttpClient` インスタンスは `@inject` ディレクティブを使用して挿入します。</span><span class="sxs-lookup"><span data-stu-id="80a61-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="80a61-128">以降の例では、Todo Web API によって、(CRUD) 操作の作成、読み取り、更新、および削除が処理されます。</span><span class="sxs-lookup"><span data-stu-id="80a61-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="80a61-129">これらの例は、以下を格納する `TodoItem` クラスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="80a61-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="80a61-130">ID (`Id`、`long`) &ndash; 項目の一意の ID。</span><span class="sxs-lookup"><span data-stu-id="80a61-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="80a61-131">名前 (`Name`、`string`) &ndash; 項目の名前。</span><span class="sxs-lookup"><span data-stu-id="80a61-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="80a61-132">状態 (`IsComplete`、`bool`) &ndash; Todo 項目が終了したかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="80a61-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="80a61-133">JSON のヘルパー メソッドは、要求を URI (次の例では Web API) に送信し、応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="80a61-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="80a61-134">`GetFromJsonAsync` &ndash; HTTP GET 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="80a61-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="80a61-135">次のコードでは、コンポーネントによって `_todoItems` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="80a61-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="80a61-136">`GetTodoItems` メソッドは、コンポーネントのレンダリングが終了したとき ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)) にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="80a61-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="80a61-137">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="80a61-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="80a61-138">`PostAsJsonAsync` &ndash; JSON でエンコードされたコンテンツを含め、HTTP POST 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="80a61-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="80a61-139">次のコードでは、コンポーネントのバインドされた要素によって `_newItemName` が提供されています。</span><span class="sxs-lookup"><span data-stu-id="80a61-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="80a61-140">`AddItem` メソッドは、`<button>` 要素を選択することにでトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="80a61-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="80a61-141">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="80a61-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="80a61-142">`PostAsJsonAsync` を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。</span><span class="sxs-lookup"><span data-stu-id="80a61-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="80a61-143">応答メッセージから JSON コンテンツを逆シリアル化するには、`ReadFromJsonAsync<T>` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="80a61-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="80a61-144">`PutAsJsonAsync` &ndash; JSON でエンコードされたコンテンツを含め、HTTP PUT 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="80a61-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="80a61-145">次のコードでは、`Name` および `IsCompleted` の `_editItem` 値が、コンポーネントのバインドされた要素によって提供されています。</span><span class="sxs-lookup"><span data-stu-id="80a61-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="80a61-146">項目の `Id` は、UI の別の部分で項目が選択され、`EditItem` が呼び出されたときに設定されます。</span><span class="sxs-lookup"><span data-stu-id="80a61-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="80a61-147">`SaveItem` メソッドは、Save `<button>` 要素を選択することでトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="80a61-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="80a61-148">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="80a61-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="80a61-149">`PutAsJsonAsync` を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。</span><span class="sxs-lookup"><span data-stu-id="80a61-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="80a61-150">応答メッセージから JSON コンテンツを逆シリアル化するには、`ReadFromJsonAsync<T>` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="80a61-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="80a61-151"><xref:System.Net.Http> には、HTTP 要求を送信し、HTTP 応答を受信するための追加の拡張メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="80a61-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="80a61-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) は、HTTP DELETE 要求を Web API に送信するために使用します。</span><span class="sxs-lookup"><span data-stu-id="80a61-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="80a61-153">次のコードでは、Delete `<button>` 要素で `DeleteItem` メソッドを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="80a61-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="80a61-154">バインドされた `<input>` 要素では、削除する項目の `id` が提供されます。</span><span class="sxs-lookup"><span data-stu-id="80a61-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="80a61-155">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="80a61-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="80a61-156">クロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="80a61-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="80a61-157">Web ページでは、ブラウザーのセキュリティにより、その Web ページを提供したドメインと異なるドメインに対して要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="80a61-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="80a61-158">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="80a61-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="80a61-159">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="80a61-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="80a61-160">オリジンが異なるエンドポイントへの要求をブラウザーから行うには、*エンドポイント*で[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="80a61-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="80a61-161">[Blazor WebAssembly サンプル アプリ (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) は、呼び出し Web API コンポーネント (*Pages/CallWebAPI.razor*) で CORS を使用する具体的な方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="80a61-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="80a61-162">他のサイトがアプリに対してクロス オリジン リソース共有 (CORS) 要求を行えるようにするには、「<xref:security/cors>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="80a61-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80a61-163">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="80a61-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="80a61-164">Kestrel HTTPS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="80a61-164">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="80a61-165">W3C におけるクロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="80a61-165">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
