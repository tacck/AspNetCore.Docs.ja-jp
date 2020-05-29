---
<span data-ttu-id="20b0b-101">title:'ASP.NET Core Blazor WebAssembly から Web API を呼び出す' author: description:'クロス オリジン リソース共有 (CORS) 要求の作成など、JSON ヘルパーを使用して Blazor WebAssembly アプリから Web API を呼び出す方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="20b0b-101">title: 'Call a web API from ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to call a web API from a Blazor WebAssembly app using JSON helpers, including making cross-origin resource sharing (CORS) requests.'</span></span>
<span data-ttu-id="20b0b-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="20b0b-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="20b0b-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="20b0b-103">'Blazor'</span></span>
- <span data-ttu-id="20b0b-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="20b0b-104">'Identity'</span></span>
- <span data-ttu-id="20b0b-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="20b0b-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="20b0b-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="20b0b-106">'Razor'</span></span>
- <span data-ttu-id="20b0b-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="20b0b-107">'SignalR' uid:</span></span> 

---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="20b0b-108">ASP.NET Core Blazor から Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="20b0b-108">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="20b0b-109">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)、[Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="20b0b-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

<span data-ttu-id="20b0b-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) アプリは、事前に構成された `HttpClient` サービスを使用して Web API を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="20b0b-111">JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) オプションを含めることができる要求は、Blazor JSON ヘルパーまたは <xref:System.Net.Http.HttpRequestMessage> を使用して作成ます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-111">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="20b0b-112">Blazor WebAssembly アプリの `HttpClient` サービスは、発行元のサーバーに要求を戻すことに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-112">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="20b0b-113">このトピックのガイダンスは、Blazor WebAssembly アプリにのみ関連します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-113">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="20b0b-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) アプリは、<xref:System.Net.Http.HttpClient> インスタンスを使用して Web API を呼び出します。これは通常、<xref:System.Net.Http.IHttpClientFactory> を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="20b0b-115">このトピックのガイダンスは、Blazor Server アプリには関係しません。</span><span class="sxs-lookup"><span data-stu-id="20b0b-115">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="20b0b-116">Blazor Server アプリの開発時には、<xref:fundamentals/http-requests> のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="20b0b-116">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="20b0b-117">[サンプルコードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([ダウンロードの方法](xref:index#how-to-download-a-sample)) &ndash;*BlazorWebAssemblySample* アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="20b0b-118">*BlazorWebAssemblySample* サンプル アプリ内の以下のコンポーネントを参照します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-118">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="20b0b-119">Web API の呼び出し (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="20b0b-119">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="20b0b-120">HTTP 要求テスター (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="20b0b-120">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="20b0b-121">パッケージ</span><span class="sxs-lookup"><span data-stu-id="20b0b-121">Packages</span></span>

<span data-ttu-id="20b0b-122">プロジェクト ファイルで [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-122">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="20b0b-123">HttpClient サービスを追加する</span><span class="sxs-lookup"><span data-stu-id="20b0b-123">Add the HttpClient service</span></span>

<span data-ttu-id="20b0b-124">`Program.Main` で、`HttpClient` サービスがまだ存在しない場合は追加します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-124">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="20b0b-125">HttpClient と JSON ヘルパー</span><span class="sxs-lookup"><span data-stu-id="20b0b-125">HttpClient and JSON helpers</span></span>

<span data-ttu-id="20b0b-126">Blazor WebAssembly アプリでは、[HttpClient](xref:fundamentals/http-requests) は、要求を要求元のサーバーに返すための構成済みのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-126">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="20b0b-127">Blazor Server アプリには、既定では `HttpClient` サービスが含まれません。</span><span class="sxs-lookup"><span data-stu-id="20b0b-127">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="20b0b-128">[HttpClient ファクトリ インフラストラクチャ](xref:fundamentals/http-requests)を使用して、アプリに `HttpClient` を提供します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-128">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="20b0b-129">`HttpClient` と JSON のヘルパーは、サード パーティの Web API エンドポイントを呼び出すためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-129">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="20b0b-130">`HttpClient` は、ブラウザーの [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) を使用して実装されており、同一生成元ポリシーの適用を含め、その制限に従います。</span><span class="sxs-lookup"><span data-stu-id="20b0b-130">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="20b0b-131">クライアントのベース アドレスは、生成元のサーバーのアドレスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-131">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="20b0b-132">`HttpClient` インスタンスは `@inject` ディレクティブを使用して挿入します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-132">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="20b0b-133">以降の例では、Todo Web API によって、(CRUD) 操作の作成、読み取り、更新、および削除が処理されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-133">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="20b0b-134">これらの例は、以下を格納する `TodoItem` クラスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-134">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="20b0b-135">ID (`Id`、`long`) &ndash; 項目の一意の ID。</span><span class="sxs-lookup"><span data-stu-id="20b0b-135">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="20b0b-136">名前 (`Name`、`string`) &ndash; 項目の名前。</span><span class="sxs-lookup"><span data-stu-id="20b0b-136">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="20b0b-137">状態 (`IsComplete`、`bool`) &ndash; Todo 項目が終了したかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-137">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="20b0b-138">JSON のヘルパー メソッドは、要求を URI (次の例では Web API) に送信し、応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-138">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="20b0b-139">`GetFromJsonAsync` &ndash; HTTP GET 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-139">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="20b0b-140">次のコードでは、コンポーネントによって `todoItems` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-140">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="20b0b-141">`GetTodoItems` メソッドは、コンポーネントのレンダリングが終了したとき ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)) にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-141">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="20b0b-142">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="20b0b-142">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="20b0b-143">`PostAsJsonAsync` &ndash; JSON でエンコードされたコンテンツを含め、HTTP POST 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-143">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="20b0b-144">次のコードでは、コンポーネントのバインドされた要素によって `newItemName` が提供されています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-144">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="20b0b-145">`AddItem` メソッドは、`<button>` 要素を選択することにでトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-145">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="20b0b-146">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="20b0b-146">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="20b0b-147">`PostAsJsonAsync` を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-147">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="20b0b-148">応答メッセージから JSON コンテンツを逆シリアル化するには、`ReadFromJsonAsync<T>` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-148">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="20b0b-149">`PutAsJsonAsync` &ndash; JSON でエンコードされたコンテンツを含め、HTTP PUT 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-149">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="20b0b-150">次のコードでは、`Name` および `IsCompleted` の `editItem` 値が、コンポーネントのバインドされた要素によって提供されています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-150">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="20b0b-151">項目の `Id` は、UI の別の部分で項目が選択され、`EditItem` が呼び出されたときに設定されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-151">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="20b0b-152">`SaveItem` メソッドは、Save `<button>` 要素を選択することでトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-152">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="20b0b-153">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="20b0b-153">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="20b0b-154">`PutAsJsonAsync` を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-154">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="20b0b-155">応答メッセージから JSON コンテンツを逆シリアル化するには、`ReadFromJsonAsync<T>` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-155">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="20b0b-156"><xref:System.Net.Http> には、HTTP 要求を送信し、HTTP 応答を受信するための追加の拡張メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-156"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="20b0b-157">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) は、HTTP DELETE 要求を Web API に送信するために使用します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-157">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="20b0b-158">次のコードでは、Delete `<button>` 要素で `DeleteItem` メソッドを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-158">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="20b0b-159">バインドされた `<input>` 要素では、削除する項目の `id` が提供されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-159">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="20b0b-160">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="20b0b-160">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="20b0b-161">IHttpClientFactory を使用する名前付き HttpClient</span><span class="sxs-lookup"><span data-stu-id="20b0b-161">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="20b0b-162"><xref:System.Net.Http.IHttpClientFactory> サービスおよび名前付き <xref:System.Net.Http.HttpClient> の構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-162"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="20b0b-163">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="20b0b-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="20b0b-164">`FetchData` コンポーネント (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="20b0b-164">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="20b0b-165">型指定された HttpClient</span><span class="sxs-lookup"><span data-stu-id="20b0b-165">Typed HttpClient</span></span>

<span data-ttu-id="20b0b-166">型指定された <xref:System.Net.Http.HttpClient> では、1 つ以上のアプリの <xref:System.Net.Http.HttpClient> インスタンス (既定または名前付き) を使用して、1 つ以上の Web API エンドポイントからデータを返します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-166">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="20b0b-167">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="20b0b-167">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

<span data-ttu-id="20b0b-168">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="20b0b-168">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="20b0b-169">コンポーネントによって型指定された `HttpClient` が挿入され、Web API が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-169">Components inject the typed `HttpClient` to call the web API.</span></span>

<span data-ttu-id="20b0b-170">`FetchData` コンポーネント (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="20b0b-170">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="20b0b-171">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="20b0b-171">Handle errors</span></span>

<span data-ttu-id="20b0b-172">Web API の操作中にエラーが発生した場合は、開発者コードで処理できます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-172">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="20b0b-173">たとえば、`GetFromJsonAsync` では、`Content-Type` が `application/json` のサーバー API からの JSON 応答が想定されています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-173">For example, `GetFromJsonAsync` expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="20b0b-174">応答が JSON 形式ではない場合、コンテンツ検証により <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-174">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="20b0b-175">次の例では、天気予報データ要求の URI エンドポイントのスペルが間違っています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-175">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="20b0b-176">URI を `WeatherForecast` にする必要がありますが、呼び出しでは `WeatherForcast` と表示されています ("e" がありません)。</span><span class="sxs-lookup"><span data-stu-id="20b0b-176">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="20b0b-177">`GetFromJsonAsync` の呼び出しでは、JSON が返されることが想定されていますが、`Content-Type` が `text/html` のサーバー上の未処理の例外に対して、サーバーから HTML が返されます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-177">The `GetFromJsonAsync` call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="20b0b-178">パスが見つからず、ミドルウェアから要求のページまたはビューを提供できないため、サーバー上で未処理の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-178">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="20b0b-179">クライアント上の `OnInitializedAsync` では、応答コンテンツが非 JSON であると検証されると <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-179">In `OnInitializedAsync` on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="20b0b-180">例外は `catch` ブロックでキャッチされます。ここでカスタム ロジックを使用してエラーをログに記録したり、わかりやすいエラー メッセージをユーザーに表示したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-180">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="20b0b-181">前の例は、デモンストレーションを目的としています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-181">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="20b0b-182">エンドポイントが存在しない場合や、サーバー上で未処理の例外が発生した場合でも、JSON を返すように Web API サーバー アプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-182">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="20b0b-183">詳細については、「<xref:blazor/handle-errors>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="20b0b-183">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="20b0b-184">クロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="20b0b-184">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="20b0b-185">Web ページでは、ブラウザーのセキュリティにより、その Web ページを提供したドメインと異なるドメインに対して要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="20b0b-185">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="20b0b-186">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="20b0b-186">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="20b0b-187">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-187">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="20b0b-188">オリジンが異なるエンドポイントへの要求をブラウザーから行うには、*エンドポイント*で[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="20b0b-188">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="20b0b-189">[Blazor WebAssembly サンプル アプリ (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) は、呼び出し Web API コンポーネント (*Pages/CallWebAPI.razor*) で CORS を使用する具体的な方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="20b0b-189">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="20b0b-190">他のサイトがアプリに対してクロス オリジン リソース共有 (CORS) 要求を行えるようにするには、「<xref:security/cors>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="20b0b-190">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20b0b-191">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="20b0b-191">Additional resources</span></span>

* <span data-ttu-id="20b0b-192"><xref:security/blazor/webassembly/additional-scenarios> &ndash; `HttpClient` を使用してセキュリティで保護された Web API 要求を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="20b0b-192"><xref:security/blazor/webassembly/additional-scenarios> &ndash; Includes coverage on using `HttpClient` to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="20b0b-193">Kestrel HTTPS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="20b0b-193">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="20b0b-194">W3C におけるクロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="20b0b-194">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
