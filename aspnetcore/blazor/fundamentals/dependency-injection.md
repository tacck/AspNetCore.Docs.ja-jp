---
title: ASP.NET Core Blazor 依存関係の挿入
author: guardrex
description: Blazor アプリでコンポーネントにサービスを挿入する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: e88a471a35e1c2be5f77407a6c594cd6a97e1737
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944368"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor 依存関係の挿入

作成者: [Rainer Stropek](https://www.timecockpit.com)、[Mike Rousos](https://github.com/mjrousos)

Blazor では、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) がサポートされています。 アプリでは、組み込みサービスをコンポーネントに挿入することにより、サービスを使用できます。 また、アプリでは、カスタム サービスを定義して登録し、DI によってアプリ全体でそれを使用できるようにすることもできます。

DI は、中央の場所で構成されたサービスにアクセスするための手法です。 これは、Blazor アプリで次のような場合に役立ちます。

* サービス クラスの 1 つのインスタンスを、多数のコンポーネント間で共有します。これは、"*シングルトン*" サービスと呼ばれます。
* 参照の抽象化を使用することで、具象サービス クラスからコンポーネントを切り離します。 たとえば、アプリ内のデータにアクセスするためのインターフェイス `IDataAccess` について考えます。 このインターフェイスは、具象クラス `DataAccess` によって実装され、アプリのサービス コンテナーにサービスとして登録されます。 コンポーネントで DI を使用して `IDataAccess` の実装を受け取ると、コンポーネントは具象型に結合されません。 たとえば単体テストでのモック実装の場合など、実装をスワップすることができます。

## <a name="default-services"></a>既定のサービス

既定のサービスは、アプリのサービス コレクションに自動的に追加されます。

| サービス | 有効期間 | 説明 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | 一時的 | URI によって識別されるリソースに HTTP 要求を送信し、そのリソースから HTTP 応答を受信するためのメソッドが提供されます。<br><br>Blazor WebAssembly アプリの <xref:System.Net.Http.HttpClient> のインスタンスでは、バックグラウンドでの HTTP トラフィックの処理にブラウザーが使用されます。<br><br>Blazor Server アプリには、既定でサービスとして構成される <xref:System.Net.Http.HttpClient> は含まれません。 Blazor Server アプリには <xref:System.Net.Http.HttpClient> を指定します。<br><br>詳細については、「<xref:blazor/call-web-api>」を参照してください。 |
| <xref:Microsoft.JSInterop.IJSRuntime> | シングルトン (Blazor WebAssembly)<br>スコープ (Blazor Server) | JavaScript の呼び出しがディスパッチされる JavaScript ランタイムのインスタンスを表します。 詳細については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | シングルトン (Blazor WebAssembly)<br>スコープ (Blazor Server) | URI とナビゲーション状態を操作するためのヘルパーが含まれます。 詳細については、「[URI およびナビゲーション状態ヘルパー](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)」を参照してください。 |

カスタム サービス プロバイダーでは、表に示されている既定のサービスは自動的に提供されません。 カスタム サービス プロバイダーを使用し、表に示されているいずれかのサービスが必要な場合は、必要なサービスを新しいサービス プロバイダーに追加します。

## <a name="add-services-to-an-app"></a>サービスをアプリに追加する

### Blazor WebAssembly

`Program.cs` の `Main` メソッドで、アプリのサービス コレクション用のサービスを構成します。 次の例では、`MyDependency` の実装が `IMyDependency` に登録されます。

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
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddTransient(sp => 
            new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });

        await builder.Build().RunAsync();
    }
}
```

ホストが構築されると、コンポーネントがレンダリングされる前に、ルート DI スコープからサービスにアクセスできるようになります。 これは、コンテンツをレンダリングする前に初期化ロジックを実行する場合に役に立ちます。

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddTransient(sp => 
            new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

また、ホストでは、アプリの中央構成インスタンスも提供されます。 前の例を基にして、天気予報サービスの URL を、既定の構成ソース (`appsettings.json` など) から `InitializeWeatherAsync` に渡します。

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddTransient(sp => 
            new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

新しいアプリを作成した後、`Startup.ConfigureServices` メソッドを調べます。

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> メソッドには、サービス記述子オブジェクト (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) のリストである <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> が渡されます。 サービスは、`ConfigureServices` メソッドでサービス コレクションにサービス記述子を提供することによって追加されます。 次の例では、`IDataAccess` インターフェイスとその具象実装 `DataAccess` での概念を示します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>サービスの有効期間

サービスは、次の表に示す有効期間で構成できます。

| 有効期間 | 説明 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | 現在、Blazor WebAssembly アプリには DI スコープの概念はありません。 `Scoped` 登録済みサービスは `Singleton` サービスのように動作します。 ただし、Blazor Server ホスティング モデルでは、`Scoped` 有効期間がサポートされています。 Blazor Server アプリでは、スコープ サービスの登録は "*接続*" にスコープされます。 このため、現在の目的がブラウザーでクライアント側を実行する場合でも、現在のユーザーにスコープする必要があるサービスの場合は、スコープ サービスを使用することが推奨されます。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI では、サービスの "*単一インスタンス*" が作成されます。 `Singleton` サービスを必要とするすべてのコンポーネントは、同じサービスのインスタンスを受け取ります。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | コンポーネントは、サービス コンテナーから `Transient` サービスのインスタンスを取得するたびに、サービスの "*新しいインスタンス*" を受け取ります。 |

DI システムは、ASP.NET Core の DI システムが基になっています。 詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。

## <a name="request-a-service-in-a-component"></a>コンポーネント内のサービスを要求する

サービスがサービス コレクションに追加された後、[\@inject](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、サービスをコンポーネントに挿入します。 [`@inject`](xref:mvc/views/razor#inject) には、次の 2 つのパラメーターがあります。

* 型:挿入するサービスの型。
* プロパティ:挿入されたアプリ サービスを受け取るプロパティの名前。 プロパティを手動で作成する必要はありません。 プロパティはコンパイラによって作成されます。

詳細については、「<xref:mvc/views/dependency-injection>」を参照してください。

異なるサービスを挿入するには、複数の [`@inject`](xref:mvc/views/razor#inject) ステートメントを使用します。

次の例は、[`@inject`](xref:mvc/views/razor#inject) を使用する方法を示しています。 `Services.IDataAccess` を実装するサービスを、コンポーネントのプロパティ `DataRepository` に挿入します。 コードによって `IDataAccess` 抽象化だけが使用されていることに注意してください。

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

内部的には、生成されたプロパティ (`DataRepository`) によって、[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性が使用されます。 通常、この属性を直接使用することはありません。 コンポーネントで基底クラスが必要であり、基底クラスで挿入されたプロパティも必要な場合は、[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性を手動で追加します。

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

基底クラスから派生されたコンポーネントでは、[`@inject`](xref:mvc/views/razor#inject) ディレクティブは必要ありません。 基底クラスの <xref:Microsoft.AspNetCore.Components.InjectAttribute> で十分です。

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>サービスで DI を使用する

複雑なサービスでは、追加のサービスが必要になる場合があります。 前の例では、`DataAccess` で <xref:System.Net.Http.HttpClient> の既定のサービスが必要になる場合があります。 [`@inject`](xref:mvc/views/razor#inject) (または [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性) は、サービスでは使用できません。 代わりに、"*コンストラクター挿入*" を使用する必要があります。 サービスのコンストラクターにパラメーターを追加することによって、必要なサービスが追加されます。 DI では、サービスを作成するときに、コンストラクターで必要なサービスが認識され、それに応じてサービスが提供されます。 次の例では、コンストラクターは DI で <xref:System.Net.Http.HttpClient> を受け取ります。 <xref:System.Net.Http.HttpClient> は既定のサービスです。

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

コンストラクター挿入の前提条件:

* DI によってすべての引数を満たすことができるコンストラクターが 1 つ存在する必要があります。 DI で満たすことができない追加のパラメーターは、既定値が指定されている場合に許可されます。
* 該当するコンストラクターは、`public` である必要があります。
* 該当するコンストラクターが 1 つ存在する必要があります。 あいまいさがある場合は、DI で例外がスローされます。

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>DI スコープを管理するためのユーティリティの基本コンポーネント クラス

ASP.NET Core アプリでは、スコープ サービスは通常、現在の要求にスコープされます。 要求が完了すると、スコープ サービスまたは一時サービスは DI システムによって破棄されます。 Blazor Server アプリでは、要求スコープはクライアント接続の期間を通して保持されるため、一時サービスとスコープ サービスが予想よりはるかに長く存続する可能性があります。 Blazor WebAssembly アプリでは、スコープ付きの有効期間で登録されたサービスはシングルトンとして扱われるため、通常の ASP.NET Core アプリのスコープ サービスより長く存続します。

> [!NOTE]
> アプリ内の破棄可能な一時サービスを検出するには、「[破棄可能な一時サービスの検出](#detect-transient-disposables)」セクションを参照してください。

Blazor アプリでサービスの有効期間を制限するには、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> 型を使用します。 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> は <xref:Microsoft.AspNetCore.Components.ComponentBase> から派生された抽象型であり、コンポーネントの有効期間に対応する DI スコープを作成します。 このスコープを使用すると、スコープ付きの有効期間で DI サービスを使用し、コンポーネントと同じ期間だけ持続させることができます。 コンポーネントが破棄されると、コンポーネントのスコープ サービス プロバイダーからのサービスも破棄されます。 これは、次のようなサービスに役立ちます。

* 一時的な有効期間が不適切であるため、コンポーネント内で再利用する必要がある。
* シングルトンの有効期間が不適切であるため、コンポーネント間で共有してはならない。

<xref:Microsoft.AspNetCore.Components.OwningComponentBase> 型には、使用できるバージョンが 2 つあります。

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> は、<xref:Microsoft.AspNetCore.Components.ComponentBase> 型の抽象的で破棄可能な子であり、<xref:System.IServiceProvider>型の保護された <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロパティがあります。 このプロバイダーを使用すると、コンポーネントの有効期間にスコープが設定されているサービスを解決できます。

  [`@inject`](xref:mvc/views/razor#inject) または [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性を使用してコンポーネントに挿入された DI サービスは、コンポーネントのスコープでは作成されません。 コンポーネントのスコープを使用するには、<xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> または <xref:System.IServiceProvider.GetService%2A> を使用してサービスを解決する必要があります。 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロバイダーを使用して解決されたすべてのサービスには、同じスコープから提供される依存関係があります。

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

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> から派生する <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> では、スコープ DI プロバイダーから `T` のインスタンスを返すプロパティ <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> が追加されます。 この型は、アプリで 1 つのプライマリ サービスをコンポーネントのスコープを使用して DI コンテナーに要求するときに、<xref:System.IServiceProvider> のインスタンスを使用せずにスコープ サービスにアクセスするための便利な方法です。 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロパティを使用できるので、必要に応じて、アプリで他の型のサービスを取得できます。

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>DI からの Entity Framework の DbContext の使用

Web アプリで DI から取得する一般的なサービスの型の 1 つは、Entity Framework (EF) の <xref:Microsoft.EntityFrameworkCore.DbContext> オブジェクトです。 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> を使用して EF サービスを登録すると、既定ではスコープ サービスとして <xref:Microsoft.EntityFrameworkCore.DbContext> が追加されます。 スコープ サービスとして登録すると、<xref:Microsoft.EntityFrameworkCore.DbContext> のインスタンスの有効期間が長くなり、アプリ全体で共有されるため、Blazor アプリで問題が発生する可能性があります。 <xref:Microsoft.EntityFrameworkCore.DbContext> はスレッドセーフではなく、同時に使用することはできません。

アプリによっては、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> を使用して <xref:Microsoft.EntityFrameworkCore.DbContext> のスコープを 1 つのコンポーネントに限定することで、問題が解決する "*場合があります*"。 コンポーネントで <xref:Microsoft.EntityFrameworkCore.DbContext> が並列に使用されていない場合は、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> からコンポーネントを派生させ、<xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> から <xref:Microsoft.EntityFrameworkCore.DbContext> を取得すれば、次のことが保証されるため、他には何も必要ありません。

* 個別のコンポーネントで <xref:Microsoft.EntityFrameworkCore.DbContext> が共有されません。
* <xref:Microsoft.EntityFrameworkCore.DbContext> は、それに依存するコンポーネントと同じ期間だけ存在します。

1 つのコンポーネントで <xref:Microsoft.EntityFrameworkCore.DbContext> が同時に使用される可能性がある場合は (たとえば、ユーザーがボタンを選択するたび)、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> を使用しても、EF の同時操作に関する問題を回避することはできません。 その場合は、論理 EF 操作ごとに別の <xref:Microsoft.EntityFrameworkCore.DbContext> を使用します。 次のいずれかの方法を使用します。

* 引数として <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> を使用して、<xref:Microsoft.EntityFrameworkCore.DbContext> を直接作成します。これは DI から取得でき、スレッドセーフです。

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* 一時的な有効期間を使用して、サービス コンテナーに <xref:Microsoft.EntityFrameworkCore.DbContext> を登録します。
  * コンテキストを登録するときに、<xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> を使用します。 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 拡張メソッドは、<xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> 型の 2 つの省略可能なパラメーターを受け取ります。 この方法を使用するには、`contextLifetime` パラメーターだけを <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> にする必要があります。 `optionsLifetime` は、既定値である <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> のままにできます。

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * 一時的な <xref:Microsoft.EntityFrameworkCore.DbContext> は、複数の EF 操作を並列に実行しないコンポーネントに、通常通りに ([`@inject`](xref:mvc/views/razor#inject) を使用して) 挿入できます。 複数の EF 操作を同時に実行する可能性がある場合は、<xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> を使用して、並列操作ごとに個別の <xref:Microsoft.EntityFrameworkCore.DbContext> オブジェクトを要求できます。

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="detect-transient-disposables"></a>破棄可能な一時サービスの検出

次の例は、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> を使用する必要があるアプリ内の破棄可能な一時サービスを検出する方法を示しています。 詳細については、「[DI スコープを管理するためのユーティリティの基本コンポーネント クラス](#utility-base-component-classes-to-manage-a-di-scope)」セクションを参照してください。

### Blazor WebAssembly

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

次の例では、`TransientDisposable` が検出されます (`Program.cs`)。

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

`Program`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

次の例では、`TransientDependency` が検出されます (`Startup.cs`)。

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/dependency-injection>
* [一時的なインスタンスと共有インスタンスのための `IDisposable` ガイダンス](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
