---
title: ASP.NET Core Blazor 依存関係の挿入
author: guardrex
description: Blazor アプリでコンポーネントにサービスを挿入する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: c68deb5237754872e11bfd9c83275b9a3b147319
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556516"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="a0a9c-103">ASP.NET Core Blazor 依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="a0a9c-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="a0a9c-104">作成者: [Rainer Stropek](https://www.timecockpit.com)、[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="a0a9c-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="a0a9c-105">Blazor では、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a0a9c-106">アプリでは、組み込みサービスをコンポーネントに挿入することにより、サービスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="a0a9c-107">また、アプリでは、カスタム サービスを定義して登録し、DI によってアプリ全体でそれを使用できるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="a0a9c-108">DI は、中央の場所で構成されたサービスにアクセスするための手法です。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="a0a9c-109">これは、Blazor アプリで次のような場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="a0a9c-110">サービス クラスの 1 つのインスタンスを、多数のコンポーネント間で共有します。これは、"*シングルトン*" サービスと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="a0a9c-111">参照の抽象化を使用することで、具象サービス クラスからコンポーネントを切り離します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="a0a9c-112">たとえば、アプリ内のデータにアクセスするためのインターフェイス `IDataAccess` について考えます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="a0a9c-113">このインターフェイスは、具象クラス `DataAccess` によって実装され、アプリのサービス コンテナーにサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="a0a9c-114">コンポーネントで DI を使用して `IDataAccess` の実装を受け取ると、コンポーネントは具象型に結合されません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="a0a9c-115">たとえば単体テストでのモック実装の場合など、実装をスワップすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="a0a9c-116">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="a0a9c-116">Default services</span></span>

<span data-ttu-id="a0a9c-117">既定のサービスは、アプリのサービス コレクションに自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="a0a9c-118">サービス</span><span class="sxs-lookup"><span data-stu-id="a0a9c-118">Service</span></span> | <span data-ttu-id="a0a9c-119">有効期間</span><span class="sxs-lookup"><span data-stu-id="a0a9c-119">Lifetime</span></span> | <span data-ttu-id="a0a9c-120">説明</span><span class="sxs-lookup"><span data-stu-id="a0a9c-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="a0a9c-121">スコープ</span><span class="sxs-lookup"><span data-stu-id="a0a9c-121">Scoped</span></span> | <span data-ttu-id="a0a9c-122">URI によって識別されるリソースに HTTP 要求を送信し、そのリソースから HTTP 応答を受信するためのメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="a0a9c-123">Blazor WebAssembly アプリの <xref:System.Net.Http.HttpClient> のインスタンスでは、バックグラウンドでの HTTP トラフィックの処理にブラウザーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="a0a9c-124">Blazor Server アプリには、既定でサービスとして構成される <xref:System.Net.Http.HttpClient> は含まれません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="a0a9c-125">Blazor Server アプリには <xref:System.Net.Http.HttpClient> を指定します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="a0a9c-126">詳細については、「<xref:blazor/call-web-api>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="a0a9c-127"><xref:System.Net.Http.HttpClient> は、シングルトンではなく、スコープ サービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="a0a9c-128">詳細については、「[サービスの有効期間](#service-lifetime)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="a0a9c-129">シングルトン (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="a0a9c-129">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="a0a9c-130">スコープ (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="a0a9c-130">Scoped (Blazor Server)</span></span> | <span data-ttu-id="a0a9c-131">JavaScript の呼び出しがディスパッチされる JavaScript ランタイムのインスタンスを表します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="a0a9c-132">詳細については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="a0a9c-133">シングルトン (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="a0a9c-133">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="a0a9c-134">スコープ (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="a0a9c-134">Scoped (Blazor Server)</span></span> | <span data-ttu-id="a0a9c-135">URI とナビゲーション状態を操作するためのヘルパーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="a0a9c-136">詳細については、「[URI およびナビゲーション状態ヘルパー](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="a0a9c-137">カスタム サービス プロバイダーでは、表に示されている既定のサービスは自動的に提供されません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="a0a9c-138">カスタム サービス プロバイダーを使用し、表に示されているいずれかのサービスが必要な場合は、必要なサービスを新しいサービス プロバイダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="a0a9c-139">サービスをアプリに追加する</span><span class="sxs-lookup"><span data-stu-id="a0a9c-139">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="a0a9c-140">`Program.cs` の `Main` メソッドで、アプリのサービス コレクション用のサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="a0a9c-141">次の例では、`MyDependency` の実装が `IMyDependency` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<IMyDependency, MyDependency>();

        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="a0a9c-142">ホストが構築されると、コンポーネントがレンダリングされる前に、ルート DI スコープからサービスにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="a0a9c-143">これは、コンテンツをレンダリングする前に初期化ロジックを実行する場合に役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-143">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="a0a9c-144">また、ホストでは、アプリの中央構成インスタンスも提供されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="a0a9c-145">前の例を基にして、天気予報サービスの URL を、既定の構成ソース (`appsettings.json` など) から `InitializeWeatherAsync` に渡します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="a0a9c-146">新しいアプリを作成した後、`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="a0a9c-147"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> メソッドには、サービス記述子オブジェクト (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) のリストである <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> が渡されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="a0a9c-148">サービスは、`ConfigureServices` メソッドでサービス コレクションにサービス記述子を提供することによって追加されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="a0a9c-149">次の例では、`IDataAccess` インターフェイスとその具象実装 `DataAccess` での概念を示します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="a0a9c-150">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="a0a9c-150">Service lifetime</span></span>

<span data-ttu-id="a0a9c-151">サービスは、次の表に示す有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="a0a9c-152">有効期間</span><span class="sxs-lookup"><span data-stu-id="a0a9c-152">Lifetime</span></span> | <span data-ttu-id="a0a9c-153">説明</span><span class="sxs-lookup"><span data-stu-id="a0a9c-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="a0a9c-154">現在、Blazor WebAssembly アプリには DI スコープの概念はありません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="a0a9c-155">`Scoped` 登録済みサービスは `Singleton` サービスのように動作します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-155">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="a0a9c-156">Blazor Server ホスティング モデルでは、HTTP 要求間で `Scoped` 有効期間がサポートされていますが、クライアントに読み込まれるコンポーネント間での SingalR 接続/回線メッセージ間ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-156">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SingalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="a0a9c-157">アプリの Razor ページまたは MVC の部分では、スコープ付きサービスが通常どおりに処理され、ページまたはビュー間を移動するとき、またはページやビューからコンポーネントに移動するときに、"*各 HTTP 要求*" に対してサービスが再作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-157">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="a0a9c-158">クライアント上のコンポーネント間を移動するときは、スコープ付きサービスは再構築されません。この場合、サーバーとの通信は、HTTP 要求ではなく、ユーザーの回線の SignalR 接続を介して行われます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-158">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="a0a9c-159">次のクライアント上のコンポーネント シナリオでは、ユーザー用に新しい回線が作成されるため、スコープ付きサービスは再構築されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-159">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="a0a9c-160">ユーザーがブラウザーのウィンドウを閉じる場合。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-160">The user closes the browser's window.</span></span> <span data-ttu-id="a0a9c-161">ユーザーは新しいウィンドウを開き、アプリに戻ります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-161">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="a0a9c-162">ユーザーが、ブラウザー ウィンドウでアプリの最後のタブを閉じる場合。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-162">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="a0a9c-163">ユーザーは新しいタブを開き、アプリに戻ります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-163">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="a0a9c-164">ユーザーが、ブラウザーの再読み込みまたは更新ボタンを選択する場合。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-164">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="a0a9c-165">Blazor Server アプリのスコープ付きサービス間でユーザー状態を保持する方法の詳細については、「<xref:blazor/hosting-models?pivots=server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-165">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="a0a9c-166">DI では、サービスの "*単一インスタンス*" が作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-166">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="a0a9c-167">`Singleton` サービスを必要とするすべてのコンポーネントは、同じサービスのインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-167">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="a0a9c-168">コンポーネントは、サービス コンテナーから `Transient` サービスのインスタンスを取得するたびに、サービスの "*新しいインスタンス*" を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-168">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="a0a9c-169">DI システムは、ASP.NET Core の DI システムが基になっています。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-169">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="a0a9c-170">詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-170">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="a0a9c-171">コンポーネント内のサービスを要求する</span><span class="sxs-lookup"><span data-stu-id="a0a9c-171">Request a service in a component</span></span>

<span data-ttu-id="a0a9c-172">サービスがサービス コレクションに追加された後、[\@inject](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、サービスをコンポーネントに挿入します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-172">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="a0a9c-173">[`@inject`](xref:mvc/views/razor#inject) には、次の 2 つのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-173">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="a0a9c-174">型:挿入するサービスの型。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-174">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="a0a9c-175">プロパティ:挿入されたアプリ サービスを受け取るプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-175">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="a0a9c-176">プロパティを手動で作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-176">The property doesn't require manual creation.</span></span> <span data-ttu-id="a0a9c-177">プロパティはコンパイラによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-177">The compiler creates the property.</span></span>

<span data-ttu-id="a0a9c-178">詳細については、「<xref:mvc/views/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-178">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="a0a9c-179">異なるサービスを挿入するには、複数の [`@inject`](xref:mvc/views/razor#inject) ステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-179">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="a0a9c-180">次の例は、[`@inject`](xref:mvc/views/razor#inject) を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-180">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="a0a9c-181">`Services.IDataAccess` を実装するサービスを、コンポーネントのプロパティ `DataRepository` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-181">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="a0a9c-182">コードによって `IDataAccess` 抽象化だけが使用されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-182">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="a0a9c-183">内部的には、生成されたプロパティ (`DataRepository`) によって、[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-183">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="a0a9c-184">通常、この属性を直接使用することはありません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-184">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="a0a9c-185">コンポーネントで基底クラスが必要であり、基底クラスで挿入されたプロパティも必要な場合は、[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性を手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-185">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="a0a9c-186">基底クラスから派生されたコンポーネントでは、[`@inject`](xref:mvc/views/razor#inject) ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-186">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="a0a9c-187">基底クラスの <xref:Microsoft.AspNetCore.Components.InjectAttribute> で十分です。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-187">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="a0a9c-188">サービスで DI を使用する</span><span class="sxs-lookup"><span data-stu-id="a0a9c-188">Use DI in services</span></span>

<span data-ttu-id="a0a9c-189">複雑なサービスでは、追加のサービスが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-189">Complex services might require additional services.</span></span> <span data-ttu-id="a0a9c-190">前の例では、`DataAccess` で <xref:System.Net.Http.HttpClient> の既定のサービスが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-190">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="a0a9c-191">[`@inject`](xref:mvc/views/razor#inject) (または [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性) は、サービスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-191">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="a0a9c-192">代わりに、"*コンストラクター挿入*" を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-192">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="a0a9c-193">サービスのコンストラクターにパラメーターを追加することによって、必要なサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-193">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="a0a9c-194">DI では、サービスを作成するときに、コンストラクターで必要なサービスが認識され、それに応じてサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-194">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="a0a9c-195">次の例では、コンストラクターは DI で <xref:System.Net.Http.HttpClient> を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-195">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="a0a9c-196"><xref:System.Net.Http.HttpClient> は既定のサービスです。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-196"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="a0a9c-197">コンストラクター挿入の前提条件:</span><span class="sxs-lookup"><span data-stu-id="a0a9c-197">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="a0a9c-198">DI によってすべての引数を満たすことができるコンストラクターが 1 つ存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-198">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="a0a9c-199">DI で満たすことができない追加のパラメーターは、既定値が指定されている場合に許可されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-199">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="a0a9c-200">該当するコンストラクターは、`public` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-200">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="a0a9c-201">該当するコンストラクターが 1 つ存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-201">One applicable constructor must exist.</span></span> <span data-ttu-id="a0a9c-202">あいまいさがある場合は、DI で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-202">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="a0a9c-203">DI スコープを管理するためのユーティリティの基本コンポーネント クラス</span><span class="sxs-lookup"><span data-stu-id="a0a9c-203">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="a0a9c-204">ASP.NET Core アプリでは、スコープ サービスは通常、現在の要求にスコープされます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-204">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="a0a9c-205">要求が完了すると、スコープ サービスまたは一時サービスは DI システムによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-205">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="a0a9c-206">Blazor Server アプリでは、要求スコープはクライアント接続の期間を通して保持されるため、一時サービスとスコープ サービスが予想よりはるかに長く存続する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-206">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="a0a9c-207">Blazor WebAssembly アプリでは、スコープ付きの有効期間で登録されたサービスはシングルトンとして扱われるため、通常の ASP.NET Core アプリのスコープ サービスより長く存続します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-207">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="a0a9c-208">アプリ内の破棄可能な一時サービスを検出するには、「[破棄可能な一時サービスの検出](#detect-transient-disposables)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-208">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="a0a9c-209">Blazor アプリでサービスの有効期間を制限するには、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> 型を使用します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-209">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="a0a9c-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> は <xref:Microsoft.AspNetCore.Components.ComponentBase> から派生された抽象型であり、コンポーネントの有効期間に対応する DI スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="a0a9c-211">このスコープを使用すると、スコープ付きの有効期間で DI サービスを使用し、コンポーネントと同じ期間だけ持続させることができます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-211">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="a0a9c-212">コンポーネントが破棄されると、コンポーネントのスコープ サービス プロバイダーからのサービスも破棄されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-212">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="a0a9c-213">これは、次のようなサービスに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-213">This can be useful for services that:</span></span>

* <span data-ttu-id="a0a9c-214">一時的な有効期間が不適切であるため、コンポーネント内で再利用する必要がある。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-214">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="a0a9c-215">シングルトンの有効期間が不適切であるため、コンポーネント間で共有してはならない。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-215">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="a0a9c-216"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 型には、使用できるバージョンが 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-216">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="a0a9c-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> は、<xref:Microsoft.AspNetCore.Components.ComponentBase> 型の抽象的で破棄可能な子であり、<xref:System.IServiceProvider>型の保護された <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="a0a9c-218">このプロバイダーを使用すると、コンポーネントの有効期間にスコープが設定されているサービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-218">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="a0a9c-219">[`@inject`](xref:mvc/views/razor#inject) または [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性を使用してコンポーネントに挿入された DI サービスは、コンポーネントのスコープでは作成されません。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-219">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="a0a9c-220">コンポーネントのスコープを使用するには、<xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> または <xref:System.IServiceProvider.GetService%2A> を使用してサービスを解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-220">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="a0a9c-221"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロバイダーを使用して解決されたすべてのサービスには、同じスコープから提供される依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-221">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="a0a9c-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> から派生する <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> では、スコープ DI プロバイダーから `T` のインスタンスを返すプロパティ <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> が追加されます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="a0a9c-223">この型は、アプリで 1 つのプライマリ サービスをコンポーネントのスコープを使用して DI コンテナーに要求するときに、<xref:System.IServiceProvider> のインスタンスを使用せずにスコープ サービスにアクセスするための便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-223">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="a0a9c-224"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロパティを使用できるので、必要に応じて、アプリで他の型のサービスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-224">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="a0a9c-225">DI からの Entity Framework Core (EF Core) DbContext の使用</span><span class="sxs-lookup"><span data-stu-id="a0a9c-225">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="a0a9c-226">詳細については、「<xref:blazor/blazor-server-ef-core>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-226">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="a0a9c-227">破棄可能な一時サービスの検出</span><span class="sxs-lookup"><span data-stu-id="a0a9c-227">Detect transient disposables</span></span>

<span data-ttu-id="a0a9c-228">次の例は、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> を使用する必要があるアプリ内の破棄可能な一時サービスを検出する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-228">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="a0a9c-229">詳細については、「[DI スコープを管理するためのユーティリティの基本コンポーネント クラス](#utility-base-component-classes-to-manage-a-di-scope)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-229">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="a0a9c-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="a0a9c-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="a0a9c-231">次の例では、`TransientDisposable` が検出されます (`Program.cs`)。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-231">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

### Blazor Server

<span data-ttu-id="a0a9c-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="a0a9c-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="a0a9c-233">`Program`:</span><span class="sxs-lookup"><span data-stu-id="a0a9c-233">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="a0a9c-234">次の例では、`TransientDependency` が検出されます (`Startup.cs`)。</span><span class="sxs-lookup"><span data-stu-id="a0a9c-234">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="a0a9c-235">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a0a9c-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="a0a9c-236">一時的なインスタンスと共有インスタンスのための `IDisposable` ガイダンス</span><span class="sxs-lookup"><span data-stu-id="a0a9c-236">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
