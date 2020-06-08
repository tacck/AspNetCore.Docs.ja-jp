---
title: 'title:ASP.NET Core での依存関係の挿入 author: rick-anderson description:ASP.NET Core で依存関係の挿入を実装する方法とそれを使う方法について説明します。'
author: rick-anderson
description: "monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date:05/14/2020 no-loc:"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: db0a23e2db34de60308ea9be021a190278dee4aa
ms.sourcegitcommit: 5fe724d143825ca799e5bd03fb45b632ea4aa7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84271905"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="00a51-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="00a51-103">'Blazor'</span></span>

<span data-ttu-id="00a51-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="00a51-104">'Identity'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="00a51-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="00a51-105">'Let's Encrypt'</span></span>

<span data-ttu-id="00a51-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="00a51-106">'Razor'</span></span>

<span data-ttu-id="00a51-107">'SignalR' uid: fundamentals/dependency-injection</span><span class="sxs-lookup"><span data-stu-id="00a51-107">'SignalR' uid: fundamentals/dependency-injection</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="00a51-108">ASP.NET Core での依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="00a51-108">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="00a51-109">作成者: [Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com)、[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="00a51-109">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span> <span data-ttu-id="00a51-110">ASP.NET Core では依存関係の挿入 (DI) ソフトウェア設計パターンがサポートされています。これは、クラスとその依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための手法です。</span><span class="sxs-lookup"><span data-stu-id="00a51-110">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="00a51-111">MVC コントローラー内部における依存関係の挿入に固有の情報については、<xref:mvc/controllers/dependency-injection> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="00a51-111">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="00a51-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="00a51-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="00a51-113">依存関係の挿入の概要</span><span class="sxs-lookup"><span data-stu-id="00a51-113">Overview of dependency injection</span></span> <span data-ttu-id="00a51-114">"*依存関係*" とは、他のオブジェクトが必要とする任意のオブジェクトのことです。</span><span class="sxs-lookup"><span data-stu-id="00a51-114">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="00a51-115">アプリ内の他のクラスが依存している、次の `WriteMessage` メソッドを備えた `MyDependency` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="00a51-115">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="00a51-116">クラスで `WriteMessage` メソッドを使用できるようにするために、`MyDependency` クラスのインスタンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="00a51-116">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="00a51-117">`MyDependency` クラスは `IndexModel` クラスの依存関係です。</span><span class="sxs-lookup"><span data-stu-id="00a51-117">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="00a51-118">このクラスは `MyDependency` のインスタンスを作成し、これに直接依存しています。</span><span class="sxs-lookup"><span data-stu-id="00a51-118">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="00a51-119">コードの依存関係 (前の例など) には問題が含まれ、次の理由から回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-119">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="00a51-120">`MyDependency` を別の実装で置き換えるには、クラスを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-120">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="00a51-121">`MyDependency` が依存関係を含んでいる場合、これらはクラスによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-121">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="00a51-122">複数のクラスが `MyDependency` に依存している大規模なプロジェクトでは、構成コードがアプリ全体に分散するようになります。</span><span class="sxs-lookup"><span data-stu-id="00a51-122">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="00a51-123">このような実装では、単体テストを行うことが困難です。</span><span class="sxs-lookup"><span data-stu-id="00a51-123">This implementation is difficult to unit test.</span></span> <span data-ttu-id="00a51-124">アプリはモックまたはスタブの `MyDependency` クラスを使用する必要がありますが、この方法では不可能です。</span><span class="sxs-lookup"><span data-stu-id="00a51-124">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="00a51-125">依存関係の挿入は、次の方法によってこれらの問題に対応します。</span><span class="sxs-lookup"><span data-stu-id="00a51-125">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="00a51-126">依存関係の実装を抽象化するための、インターフェイスまたは基底クラスの使用。</span><span class="sxs-lookup"><span data-stu-id="00a51-126">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="00a51-127">サービス コンテナー内の依存関係の登録。</span><span class="sxs-lookup"><span data-stu-id="00a51-127">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="00a51-128">ASP.NET Core には、組み込みのサービス コンテナー <xref:System.IServiceProvider> が用意されています。</span><span class="sxs-lookup"><span data-stu-id="00a51-128">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="00a51-129">サービスはアプリの `Startup.ConfigureServices` メソッドに登録されています。</span><span class="sxs-lookup"><span data-stu-id="00a51-129">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="00a51-130">サービスを使用するクラスのコンストラクターへの、サービスの "*挿入*"。</span><span class="sxs-lookup"><span data-stu-id="00a51-130">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="00a51-131">依存関係のインスタンスの作成、およびインスタンスが不要になったときの廃棄の役割を、フレームワークが担当します。</span><span class="sxs-lookup"><span data-stu-id="00a51-131">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="00a51-132">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)では、サービスがアプリに提供するメソッドが `IMyDependency` インターフェイスによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-132">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="00a51-133">このインターフェイスは、具象型 `MyDependency` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-133">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="00a51-134">`MyDependency` では、コンストラクター内で <xref:Microsoft.Extensions.Logging.ILogger`1> が要求されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-134">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="00a51-135">依存関係の挿入をチェーン形式で使用することはよくあります。</span><span class="sxs-lookup"><span data-stu-id="00a51-135">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="00a51-136">次に、要求されたそれぞれの依存関係が、それ自身の依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="00a51-136">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="00a51-137">コンテナーによってグラフ内の依存関係が解決され、完全に解決されたサービスが返されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-137">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="00a51-138">解決する必要がある依存関係の集合的なセットは、通常、"*依存関係ツリー*"、"*依存関係グラフ*"、または "*オブジェクト グラフ*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="00a51-138">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="00a51-139">`IMyDependency` と `ILogger<TCategoryName>` をサービス コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-139">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="00a51-140">`IMyDependency` は `Startup.ConfigureServices` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-140">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="00a51-141">`ILogger<TCategoryName>` はログ記録の抽象化インフラストラクチャによって登録されます。したがって、これは、フレームワークによって既定で登録される[フレームワークが提供するサービス](#framework-provided-services)です。</span><span class="sxs-lookup"><span data-stu-id="00a51-141">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="00a51-142">コンテナーでは、[(ジェネリック) オープン型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)を活用し、すべての [(ジェネリック) 構築型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)を登録する必要をなくすことで、`ILogger<TCategoryName>` を解決します。</span><span class="sxs-lookup"><span data-stu-id="00a51-142">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="00a51-143">サンプル アプリにおいて、`IMyDependency` サービスは `MyDependency` 具象型を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-143">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="00a51-144">登録によって、サービスの有効期間が 1 つの要求の有効期間に範囲設定されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-144">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="00a51-145">[サービスの有効期間](#service-lifetimes)については、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="00a51-145">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="00a51-146">各 `services.Add{SERVICE_NAME}` 拡張メソッドは、サービスを追加 (および場合によっては構成) します。</span><span class="sxs-lookup"><span data-stu-id="00a51-146">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="00a51-147">たとえば、`services.AddMvc()` はサービスの Razor Pages と必須の MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="00a51-147">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="00a51-148">アプリをこの規則に従わせることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="00a51-148">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="00a51-149">拡張メソッドを [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 名前空間に配置して、サービス登録のグループをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="00a51-149">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="00a51-150">サービスのコンストラクターで[ビルトイン型](/dotnet/csharp/language-reference/keywords/built-in-types-table) (`string` など) が必要な場合は、[構成](xref:fundamentals/configuration/index)や[オプション パターン](xref:fundamentals/configuration/options)を使って型を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="00a51-150">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="00a51-151">クラスのコンストラクター経由でサービスのインスタンスが要求されます。サービスはプライベート フィールドに割り当てられて使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-151">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="00a51-152">このフィールドは、クラス全体で必要に応じてサービスにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-152">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="00a51-153">サンプル アプリでは、`IMyDependency` インスタンスが要求され、サービスの `WriteMessage` メソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-153">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="00a51-154">Startup に挿入されるサービス</span><span class="sxs-lookup"><span data-stu-id="00a51-154">Services injected into Startup</span></span> <span data-ttu-id="00a51-155">汎用ホスト (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) を使用すると、次のサービスの種類のみを `Startup` コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-155">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="00a51-156">サービスは `Startup.Configure` に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-156">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="00a51-157">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-157">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="00a51-158">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="00a51-158">Framework-provided services</span></span> | <span data-ttu-id="00a51-159">`Startup.ConfigureServices` メソッドでは、Entity Framework Core や ASP.NET Core MVC のようなプラットフォーム機能など、アプリが使うサービスを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-159">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="00a51-160">最初に、`ConfigureServices` に提供される `IServiceCollection` には、フレームワークによって定義されたサービスがあります ([ホストの構成方法](xref:fundamentals/index#host)によって異なります)。</span><span class="sxs-lookup"><span data-stu-id="00a51-160">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="00a51-161">ASP.NET Core テンプレートに基づくアプリが、フレームワークによって登録された何百ものサービスを持つことも珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-161">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="00a51-162">次の表に、フレームワークによって登録されたサービスのごく一部のサンプルを示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-162">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="00a51-163">サービスの種類</span><span class="sxs-lookup"><span data-stu-id="00a51-163">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="00a51-164">有効期間</span><span class="sxs-lookup"><span data-stu-id="00a51-164">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="00a51-165">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-165">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="00a51-166">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-166">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="00a51-167">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="00a51-168">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="00a51-169">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-169">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="00a51-170">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-170">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="00a51-171">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-171">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="00a51-172">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="00a51-173">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="00a51-174">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-174">Singleton</span></span>

<span data-ttu-id="00a51-175">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-175">Transient</span></span> <span data-ttu-id="00a51-176">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-176">Singleton</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="00a51-177">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-177">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="00a51-178">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-178">Singleton</span></span>

<span data-ttu-id="00a51-179">拡張メソッドを使用した追加サービスの登録する</span><span class="sxs-lookup"><span data-stu-id="00a51-179">Register additional services with extension methods</span></span> <span data-ttu-id="00a51-180">サービス (および必要であればサービスが依存するサービス) を登録するためにサービス コレクションの拡張メソッドを使用できる場合は、1 つの `Add{SERVICE_NAME}` 拡張メソッドを使用してそのサービスが必要とするすべてのサービスを登録することが規則です。</span><span class="sxs-lookup"><span data-stu-id="00a51-180">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="00a51-181">次のコードは、拡張メソッド [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) と <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> を使用して、コンテナーに追加のサービスを追加する方法の例です。</span><span class="sxs-lookup"><span data-stu-id="00a51-181">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="00a51-182">詳細については、API ドキュメントにある <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> クラスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-182">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="00a51-183">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="00a51-183">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="00a51-184">登録される各サービスの適切な有効期間を選択します。</span><span class="sxs-lookup"><span data-stu-id="00a51-184">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="00a51-185">ASP.NET Core サービスは、次の有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-185">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="00a51-186">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-186">Transient</span></span> <span data-ttu-id="00a51-187">有効期間が一時的なサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) は、サービス コンテナーから要求されるたびに作成されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-187">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="00a51-188">この有効期間は、軽量でステートレスのサービスに最適です。</span><span class="sxs-lookup"><span data-stu-id="00a51-188">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="00a51-189">スコープ</span><span class="sxs-lookup"><span data-stu-id="00a51-189">Scoped</span></span>

<span data-ttu-id="00a51-190">有効期間がスコープのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) は、クライアント要求 (接続) ごとに 1 回作成されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-190">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="00a51-191">ミドルウェアでスコープ サービスを使用している場合、サービスを `Invoke` または `InvokeAsync` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="00a51-191">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="00a51-192">コンストラクターを使用して挿入すると、サービスがシングルトンのように動作するよう強制されるので、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用した挿入は行わないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-192">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="00a51-193">詳細については、「<xref:fundamentals/middleware/write#per-request-middleware-dependencies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-193">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a51-194">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-194">Singleton</span></span> <span data-ttu-id="00a51-195">有効期間がシングルトンのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) は、最初に要求されたときに作成されます (または、`Startup.ConfigureServices` が実行されて、サービス登録でインスタンスが指定された場合)。</span><span class="sxs-lookup"><span data-stu-id="00a51-195">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="00a51-196">以降の要求は、すべて同じインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="00a51-196">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="00a51-197">アプリをシングルトンで動作させる必要がある場合は、サービス コンテナーによるサービスの有効期間の管理を許可することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="00a51-197">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="00a51-198">クラス内のオブジェクトの有効期間を管理するために、シングルトンの設計パターンを実装してユーザー コードを提供しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-198">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="00a51-199">シングルトンからスコープ サービスを解決するのは危険です。</span><span class="sxs-lookup"><span data-stu-id="00a51-199">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="00a51-200">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-200">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="00a51-201">サービス登録メソッド</span><span class="sxs-lookup"><span data-stu-id="00a51-201">Service registration methods</span></span> | <span data-ttu-id="00a51-202">サービス登録拡張メソッドでは、特定のシナリオで役立つオーバーロードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-202">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="00a51-203">メソッド</span><span class="sxs-lookup"><span data-stu-id="00a51-203">Method</span></span> | <span data-ttu-id="00a51-204">自動</span><span class="sxs-lookup"><span data-stu-id="00a51-204">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="00a51-205">object</span><span class="sxs-lookup"><span data-stu-id="00a51-205">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="00a51-206">破棄</span><span class="sxs-lookup"><span data-stu-id="00a51-206">disposal</span></span> | <span data-ttu-id="00a51-207">複数</span><span class="sxs-lookup"><span data-stu-id="00a51-207">Multiple</span></span> | <span data-ttu-id="00a51-208">実装</span><span class="sxs-lookup"><span data-stu-id="00a51-208">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="00a51-209">引数を渡す</span><span class="sxs-lookup"><span data-stu-id="00a51-209">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="00a51-210">例:</span><span class="sxs-lookup"><span data-stu-id="00a51-210">Example:</span></span> | <span data-ttu-id="00a51-211">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-211">Yes</span></span> | <span data-ttu-id="00a51-212">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="00a51-213">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-213">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="00a51-214">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-214">Examples:</span></span> | <span data-ttu-id="00a51-215">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-215">Yes</span></span> | <span data-ttu-id="00a51-216">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-216">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="00a51-217">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-217">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="00a51-218">例:</span><span class="sxs-lookup"><span data-stu-id="00a51-218">Example:</span></span> | <span data-ttu-id="00a51-219">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-219">Yes</span></span> | <span data-ttu-id="00a51-220">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-220">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="00a51-221">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-221">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="00a51-222">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-222">Examples:</span></span> | <span data-ttu-id="00a51-223">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-223">No</span></span> | <span data-ttu-id="00a51-224">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-224">Yes</span></span> |

<span data-ttu-id="00a51-225">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-225">Yes</span></span> <span data-ttu-id="00a51-226">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-226">Examples:</span></span>

<span data-ttu-id="00a51-227">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-227">No</span></span>

<span data-ttu-id="00a51-228">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-228">No</span></span> <span data-ttu-id="00a51-229">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-229">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="00a51-230">型の廃棄の詳細については、「[サービスの破棄](#disposal-of-services)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-230">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="00a51-231">実装が複数の場合の一般的なシナリオとしては、[テスト用に型のモックを作成](xref:test/integration-tests#inject-mock-services)します。</span><span class="sxs-lookup"><span data-stu-id="00a51-231">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="00a51-232">`TryAdd{LIFETIME}` メソッドでは、登録された実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-232">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="00a51-233">次の例の最初の行では、`IMyDependency` に `MyDependency` を登録します。</span><span class="sxs-lookup"><span data-stu-id="00a51-233">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="00a51-234">2 行目では何も行われません。`IMyDependency` には登録された実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="00a51-234">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="00a51-235">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="00a51-235">For more information, see:</span></span> <span data-ttu-id="00a51-236">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) メソッドでは、"*同じ型*" の実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-236">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="00a51-237">複数のサービスは、`IEnumerable<{SERVICE}>` によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-237">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="00a51-238">サービスを登録するとき、開発者は同じ型のものがまだ追加されていない場合にのみインスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="00a51-238">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="00a51-239">一般に、このメソッドは、インスタンスのコピーが 2 つコンテナーに登録されるのを回避するために、ライブラリ作成者によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-239">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="00a51-240">次の例の最初の行では、`IMyDep1` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="00a51-240">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="00a51-241">2 行目では `IMyDep2` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="00a51-241">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="00a51-242">3 行目では何も行われません。`IMyDep1` には `MyDep` の登録済みの実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="00a51-242">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="00a51-243">コンストラクターの挿入の動作</span><span class="sxs-lookup"><span data-stu-id="00a51-243">Constructor injection behavior</span></span>

<span data-ttu-id="00a51-244">サービスは 2 つのメカニズムによって解決できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-244">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="00a51-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:依存関係の挿入コンテナーにサービスを登録することなくオブジェクトの作成を許可します。</span><span class="sxs-lookup"><span data-stu-id="00a51-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="00a51-246">`ActivatorUtilities` はユーザー側の抽象化 (タグ ヘルパー、MVC コントローラー、モデル バインダーなど) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-246">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="00a51-247">コンストラクターは、依存関係の挿入によって提供されない引数を受け取ることができますが、引数は既定値を割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-247">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="00a51-248">`IServiceProvider` または `ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、"*パブリック*" コンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="00a51-248">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="00a51-249">`ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、該当するコンストラクターが 1 つだけ存在することが必要です。</span><span class="sxs-lookup"><span data-stu-id="00a51-249">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="00a51-250">コンストラクターのオーバーロードはサポートされていますが、依存関係の挿入によってすべての引数を設定できるオーバーロードは 1 つしか存在できません。</span><span class="sxs-lookup"><span data-stu-id="00a51-250">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="00a51-251">Entity Framework コンテキスト</span><span class="sxs-lookup"><span data-stu-id="00a51-251">Entity Framework contexts</span></span> <span data-ttu-id="00a51-252">Entity Framework コンテキストでは通常、[範囲が指定された有効期間](#service-lifetimes)が利用され、サービス コンテナーに追加されます。これは、Web アプリ データベース操作は通常、その範囲がクライアント要求に設定されるためです。</span><span class="sxs-lookup"><span data-stu-id="00a51-252">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="00a51-253">データベース コンテキストの登録時、[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) オーバーロードによって有効期間が指定されなかった場合、既定の有効期間が範囲となります。</span><span class="sxs-lookup"><span data-stu-id="00a51-253">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="00a51-254">有効期間が与えられたサービスの場合、サービスより有効期間が短いデータベース コンテキストを使用できません。</span><span class="sxs-lookup"><span data-stu-id="00a51-254">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="00a51-255">有効期間と登録のオプション</span><span class="sxs-lookup"><span data-stu-id="00a51-255">Lifetime and registration options</span></span> <span data-ttu-id="00a51-256">有効期間と登録のオプションの違いを示すために、タスクを一意の識別子 `OperationId` を備えた操作として表す、次のインターフェイスについて考えます。</span><span class="sxs-lookup"><span data-stu-id="00a51-256">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="00a51-257">次のインターフェイスに対して操作のサービスの有効期間がどのように構成されているかに応じて、コンテナーは、クラスに要求されたときに、サービスの同じインスタンスか別のインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="00a51-257">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="00a51-258">インターフェイスは `Operation` クラス内で実装されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-258">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="00a51-259">指定されない場合、`Operation` コンストラクターは GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-259">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="00a51-260">`OperationService` が登録されます。これは、その他の `Operation` 型のそれぞれに依存します。</span><span class="sxs-lookup"><span data-stu-id="00a51-260">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="00a51-261">依存関係の挿入を経由して `OperationService` が要求される場合、これは各サービスの新しいインスタンスか、依存関係のあるサービスの有効期間に基づく既存のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="00a51-261">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="00a51-262">コンテナーからの要求時に一時的なサービスが作成されるとき、`IOperationTransient` サービスの `OperationId` と `OperationService` の `OperationId` は異なります。</span><span class="sxs-lookup"><span data-stu-id="00a51-262">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="00a51-263">`OperationService` は `IOperationTransient` クラスの新しいインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="00a51-263">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="00a51-264">新しいインスタンスは異なる `OperationId` を生成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-264">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="00a51-265">クライアント要求ごとにスコープ サービスが作成されるとき、`IOperationScoped` サービスの `OperationId` は、クライアント要求内の `OperationService` のものと同じになります。</span><span class="sxs-lookup"><span data-stu-id="00a51-265">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="00a51-266">クライアント要求間で、両方のサービスは異なる `OperationId` 値を共有します。</span><span class="sxs-lookup"><span data-stu-id="00a51-266">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="00a51-267">シングルトンとシングルトン インスタンスのサービスが 1 回作成され、すべてのクライアント要求とすべてのサービス間で使用されるとき、`OperationId` はすべてのサービス要求の間で一定です。</span><span class="sxs-lookup"><span data-stu-id="00a51-267">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="00a51-268">`Startup.ConfigureServices` では、各型が名前で指定されている有効期間に従ってコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-268">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="00a51-269">`IOperationSingletonInstance` サービスは、`Guid.Empty` の既知の ID を持った特定のインスタンスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="00a51-269">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="00a51-270">この型が使用されるタイミングは明らかです (その GUID はすべてゼロです)。</span><span class="sxs-lookup"><span data-stu-id="00a51-270">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="00a51-271">サンプル アプリでは、個々の要求内、および個々の要求間におけるオブジェクトの有効期間が示されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-271">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="00a51-272">サンプル アプリの `IndexModel` には、各種類の `IOperation` 型と `OperationService` が必要です。</span><span class="sxs-lookup"><span data-stu-id="00a51-272">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="00a51-273">次に、ページ モデル クラスとサービスのすべての `OperationId` 値が、プロパティの割り当てを通じてページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-273">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="00a51-274">2 つの要求の結果を、以下の 2 つの出力に示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-274">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="00a51-275">**最初の要求:**</span><span class="sxs-lookup"><span data-stu-id="00a51-275">**First request:**</span></span>

<span data-ttu-id="00a51-276">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="00a51-276">Controller operations:</span></span>

<span data-ttu-id="00a51-277">一時的: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="00a51-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="00a51-278">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="00a51-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="00a51-279">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="00a51-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="00a51-280">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="00a51-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="00a51-281">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="00a51-281">`OperationService` operations:</span></span>

<span data-ttu-id="00a51-282">一時的: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="00a51-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="00a51-283">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="00a51-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="00a51-284">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="00a51-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="00a51-285">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="00a51-285">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="00a51-286">**2 番目の要求:**</span><span class="sxs-lookup"><span data-stu-id="00a51-286">**Second request:**</span></span>

<span data-ttu-id="00a51-287">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="00a51-287">Controller operations:</span></span>

<span data-ttu-id="00a51-288">一時的: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="00a51-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="00a51-289">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="00a51-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="00a51-290">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="00a51-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="00a51-291">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="00a51-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="00a51-292">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="00a51-292">`OperationService` operations:</span></span>

* <span data-ttu-id="00a51-293">一時的: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="00a51-293">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="00a51-294">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="00a51-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="00a51-295">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="00a51-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="00a51-296">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="00a51-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="00a51-297">要求内および要求間で、どの `OperationId` 値が変化しているかを確認してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-297">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="00a51-298">"*一時的な*" オブジェクトは常に異なります。</span><span class="sxs-lookup"><span data-stu-id="00a51-298">*Transient* objects are always different.</span></span>

<span data-ttu-id="00a51-299">1 番目と 2 番目のクライアント要求の一時的な `OperationId` 値は、`OperationService` 操作とクライアント要求間の両方に対して異なります。</span><span class="sxs-lookup"><span data-stu-id="00a51-299">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="00a51-300">個々のサービス要求とクライアント要求に対して、新しいインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-300">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="00a51-301">*Scoped* オブジェクトは、1 つのクライアント要求内では同じですが、複数のクライアント要求間では異なります。</span><span class="sxs-lookup"><span data-stu-id="00a51-301">*Scoped* objects are the same within a client request but different across client requests.</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="00a51-302">"*シングルトン*" オブジェクトは、`Operation` インスタンスが `Startup.ConfigureServices` で提供されるかどうかに関係なく、すべてのオブジェクトとすべての要求について同じです。</span><span class="sxs-lookup"><span data-stu-id="00a51-302">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="00a51-303">main からサービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="00a51-303">Call services from main</span></span>

* <span data-ttu-id="00a51-304">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) を使用して <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成し、アプリのスコープ内のスコープ サービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="00a51-304">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="00a51-305">この方法は、起動時に初期化タスクを実行するために、スコープ サービスにアクセスするのに便利です。</span><span class="sxs-lookup"><span data-stu-id="00a51-305">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="00a51-306">次の例では、`Program.Main` で `MyScopedService` のコンテキストを取得する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-306">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="00a51-307">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="00a51-307">Scope validation</span></span>

<span data-ttu-id="00a51-308">アプリが開発環境で実行されていて、ホストを構築するために [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) が呼び出される場合、既定のサービス プロバイダーによって次を確認するためのチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-308">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="00a51-309">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="00a51-309">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="00a51-310">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="00a51-310">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="00a51-311"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-311">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="00a51-312">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-312">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="00a51-313">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-313">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="00a51-314">ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-314">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="00a51-315">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="00a51-315">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="00a51-316">詳細については、「<xref:fundamentals/host/web-host#scope-validation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-316">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="00a51-317">要求サービス</span><span class="sxs-lookup"><span data-stu-id="00a51-317">Request Services</span></span> <span data-ttu-id="00a51-318">`HttpContext` からの ASP.NET Core 要求内で使用可能なサービスは、[HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) コレクションを通じて公開されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-318">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="00a51-319">要求サービスは、アプリの一部として構成および要求されるサービスを表します。</span><span class="sxs-lookup"><span data-stu-id="00a51-319">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="00a51-320">オブジェクトで依存関係を指定すると、これらは `ApplicationServices` ではなく `RequestServices` で検出された型で満たされます。</span><span class="sxs-lookup"><span data-stu-id="00a51-320">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="00a51-321">一般に、アプリから直接これらのプロパティを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-321">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="00a51-322">代わりに、クラスのコンストラクターを介してクラスに必要な型を要求し、フレームワークに依存関係を挿入させます。</span><span class="sxs-lookup"><span data-stu-id="00a51-322">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span>
* <span data-ttu-id="00a51-323">これにより、テストしやすいクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-323">This yields classes that are easier to test.</span></span> <span data-ttu-id="00a51-324">コンストラクターのパラメーターとして依存関係を要求し、`RequestServices` コレクションにアクセスするようにします。</span><span class="sxs-lookup"><span data-stu-id="00a51-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="00a51-325">依存関係の挿入のためのサービスの設計</span><span class="sxs-lookup"><span data-stu-id="00a51-325">Design services for dependency injection</span></span> <span data-ttu-id="00a51-326">ベスト プラクティスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="00a51-326">Best practices are to:</span></span>
* <span data-ttu-id="00a51-327">各依存関係を取得するために依存関係の挿入を使用するようサービスを設計します。</span><span class="sxs-lookup"><span data-stu-id="00a51-327">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="00a51-328">ステートフル、静的クラス、およびメンバーは避けてください。</span><span class="sxs-lookup"><span data-stu-id="00a51-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="00a51-329">代わりにシングルトン サービスを使用するようにアプリを設計し、グローバルな状態を作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="00a51-329">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="00a51-330">サービス内部で依存関係のあるクラスを直接インスタンス化することを回避します。</span><span class="sxs-lookup"><span data-stu-id="00a51-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="00a51-331">直接のインスタンス化は、コードの固有の実装につながります。</span><span class="sxs-lookup"><span data-stu-id="00a51-331">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="00a51-332">アプリのクラスを、小さく、十分に要素に分割された、テストしやすいものにします。</span><span class="sxs-lookup"><span data-stu-id="00a51-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="00a51-333">クラスに含まれる挿入される依存関係が多すぎるように見える場合、これは通常、クラスが担当する役割が多すぎて、[単一責任の原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) に違反していることのサインです。</span><span class="sxs-lookup"><span data-stu-id="00a51-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="00a51-334">責任の一部を新しいクラスに移動することにより、クラスのリファクタリングを試みます。</span><span class="sxs-lookup"><span data-stu-id="00a51-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="00a51-335">Razor Pages のページ モデル クラスと MVC コントローラー クラスは、UI の問題に集中する必要があることに留意します。</span><span class="sxs-lookup"><span data-stu-id="00a51-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="00a51-336">ビジネス ルールとデータ アクセスの実装の詳細は、これらの[個別の問題](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)に適したクラスに分離する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="00a51-337">サービスの破棄</span><span class="sxs-lookup"><span data-stu-id="00a51-337">Disposal of services</span></span>
* <span data-ttu-id="00a51-338">コンテナーは、作成する <xref:System.IDisposable> 型の <xref:System.IDisposable.Dispose*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="00a51-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="00a51-339">ユーザー コードによってコンテナーに追加されたインスタンスは、自動的に破棄されません。</span><span class="sxs-lookup"><span data-stu-id="00a51-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="00a51-340">次の例では、サービスがサービス コンテナーによって作成され、自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="00a51-341">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-341">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="00a51-342">サービス インスタンスは、サービス コンテナーによって作成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-342">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="00a51-343">サービスの有効期間は、フレームワークによって認識されません。</span><span class="sxs-lookup"><span data-stu-id="00a51-343">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="00a51-344">フレームワークでは、サービスが自動的に破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-344">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="00a51-345">サービスが開発者コードで明示的に破棄されていない場合は、アプリがシャットダウンするまで保持されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="00a51-346">一時的なインスタンスと共有インスタンスのための IDisposable ガイダンス</span><span class="sxs-lookup"><span data-stu-id="00a51-346">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="00a51-347">一時的で限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="00a51-347">Transient, limited lifetime</span></span>

<span data-ttu-id="00a51-348">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="00a51-348">**Scenario**</span></span> <span data-ttu-id="00a51-349">アプリには、次のいずれかのシナリオに対して一時的な有効期間を持つ <xref:System.IDisposable> インスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="00a51-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="00a51-350">インスタンスは、ルート スコープで解決されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-350">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="00a51-351">インスタンスは、スコープが終了する前に破棄する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-351">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="00a51-352">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="00a51-352">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="00a51-353">ファクトリ パターンを使用して、親スコープの外部にインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-353">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="00a51-354">このような状況では、通常、アプリケーションには、最終的な型のコンストラクターを直接呼び出す `Create` メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="00a51-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="00a51-355">最終的な型に他の依存関係がある場合、ファクトリは次のことができます。</span><span class="sxs-lookup"><span data-stu-id="00a51-355">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="00a51-356">そのコンストラクターで <xref:System.IServiceProvider> を受け取る。</span><span class="sxs-lookup"><span data-stu-id="00a51-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="00a51-357"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> を使用してコンテナー外部でインスタンスをインスタンス化し、同時にコンテナーを依存関係のために使用する。</span><span class="sxs-lookup"><span data-stu-id="00a51-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="00a51-358">共有インスタンス、限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="00a51-358">Shared Instance, limited lifetime</span></span> <span data-ttu-id="00a51-359">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="00a51-359">**Scenario**</span></span> <span data-ttu-id="00a51-360">アプリでは、複数のサービスにわたって共有 <xref:System.IDisposable> インスタンスが必要ですが、<xref:System.IDisposable> の有効期間は限られています。</span><span class="sxs-lookup"><span data-stu-id="00a51-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="00a51-361">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="00a51-361">**Solution**</span></span>

* <span data-ttu-id="00a51-362">インスタンスをスコープ付きの有効期間に登録します。</span><span class="sxs-lookup"><span data-stu-id="00a51-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="00a51-363"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> を使用してを開始し、新しい <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="00a51-364">スコープの <xref:System.IServiceProvider> を使用して必要なサービスを取得します。</span><span class="sxs-lookup"><span data-stu-id="00a51-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="00a51-365">有効期間が終了するときにスコープを破棄します。</span><span class="sxs-lookup"><span data-stu-id="00a51-365">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="00a51-366">一般的なガイドライン</span><span class="sxs-lookup"><span data-stu-id="00a51-366">General Guidelines</span></span> <span data-ttu-id="00a51-367"><xref:System.IDisposable> インスタンスは、一時的なスコープを使用して登録しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="00a51-368">代わりに、ファクトリ パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="00a51-368">Use the factory pattern instead.</span></span> <span data-ttu-id="00a51-369">一時的またはスコープ付きの <xref:System.IDisposable> インスタンスをルート スコープ内で解決しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="00a51-370">唯一の一般的な例外は、アプリが <xref:System.IServiceProvider> を作成/再作成し、破棄する場合です。これは理想的なパターンではありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="00a51-371">DI を使用して <xref:System.IDisposable> 依存関係を受け取る場合、受信側が <xref:System.IDisposable> 自体を実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="00a51-372"><xref:System.IDisposable> 依存関係の受信側は、その依存関係で <xref:System.IDisposable.Dispose%2A> を呼び出してはなりません。</span><span class="sxs-lookup"><span data-stu-id="00a51-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="00a51-373">サービスの有効期間を制御するには、スコープを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-373">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="00a51-374">スコープは階層構造ではなく、スコープ間に特別な接続はありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="00a51-375">既定のサービス コンテナーの置換</span><span class="sxs-lookup"><span data-stu-id="00a51-375">Default service container replacement</span></span>
* <span data-ttu-id="00a51-376">組み込みのサービス コンテナーは、フレームワークと、ほとんどのコンシューマー アプリのニーズに対応することを目的としたものです。</span><span class="sxs-lookup"><span data-stu-id="00a51-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="00a51-377">組み込みコンテナーでサポートされない、以下のような特定の機能が必要な場合を除き、組み込みコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="00a51-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="00a51-378">プロパティの挿入</span><span class="sxs-lookup"><span data-stu-id="00a51-378">Property injection</span></span>

<span data-ttu-id="00a51-379">名前に基づく挿入</span><span class="sxs-lookup"><span data-stu-id="00a51-379">Injection based on name</span></span>

* <span data-ttu-id="00a51-380">子コンテナー</span><span class="sxs-lookup"><span data-stu-id="00a51-380">Child containers</span></span>
* <span data-ttu-id="00a51-381">有効期間のカスタム管理</span><span class="sxs-lookup"><span data-stu-id="00a51-381">Custom lifetime management</span></span>
* <span data-ttu-id="00a51-382">遅延初期化に対する `Func<T>` のサポート</span><span class="sxs-lookup"><span data-stu-id="00a51-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="00a51-383">規則に基づく登録</span><span class="sxs-lookup"><span data-stu-id="00a51-383">Convention-based registration</span></span>
* <span data-ttu-id="00a51-384">次のサードパーティ コンテナーは ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-384">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* [<span data-ttu-id="00a51-385">Autofac</span><span class="sxs-lookup"><span data-stu-id="00a51-385">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="00a51-386">DryIoc</span><span class="sxs-lookup"><span data-stu-id="00a51-386">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>[<span data-ttu-id="00a51-387">Grace</span><span class="sxs-lookup"><span data-stu-id="00a51-387">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)

[<span data-ttu-id="00a51-388">LightInject</span><span class="sxs-lookup"><span data-stu-id="00a51-388">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection) [<span data-ttu-id="00a51-389">Lamar</span><span class="sxs-lookup"><span data-stu-id="00a51-389">Lamar</span></span>](https://jasperfx.github.io/lamar/)

[<span data-ttu-id="00a51-390">Stashbox</span><span class="sxs-lookup"><span data-stu-id="00a51-390">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection) [<span data-ttu-id="00a51-391">Unity</span><span class="sxs-lookup"><span data-stu-id="00a51-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="00a51-392">スレッド セーフ</span><span class="sxs-lookup"><span data-stu-id="00a51-392">Thread safety</span></span>

* <span data-ttu-id="00a51-393">スレッド セーフのシングルトン サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="00a51-394">シングルトン サービスに一時的サービスへの依存関係がある場合、シングルトンによる一時的サービスの使い方によっては、一時的サービスもスレッド セーフであることが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="00a51-395">1 つのサービスのファクトリ メソッド (例: [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) に対する 2 番目の引数) をスレッド セーフにする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="00a51-396">型 (`static`) のコンストラクターのように、1 つのスレッドによって 1 回呼び出されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="00a51-397">推奨事項</span><span class="sxs-lookup"><span data-stu-id="00a51-397">Recommendations</span></span> <span data-ttu-id="00a51-398">`async/await` および `Task` ベースのサービスの解決はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="00a51-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="00a51-399">C# では非同期コンストラクターがサポートされていないため、推奨パターンは、サービスを同期的に解決した後に、非同期メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="00a51-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="00a51-400">データと構成をサービス コンテナーに直接格納しないようにします。</span><span class="sxs-lookup"><span data-stu-id="00a51-400">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="00a51-401">たとえば、通常、ユーザーのショッピング カートはサービス コンテナーに追加しません。</span><span class="sxs-lookup"><span data-stu-id="00a51-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="00a51-402">構成では、[オプション パターン](xref:fundamentals/configuration/options)を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

  <span data-ttu-id="00a51-403">同様に、他のオブジェクトへのアクセスを許可するためだけに存在する "データ ホルダー" オブジェクトは避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="00a51-404">実際のアイテムを DI 経由で要求することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="00a51-404">It's better to request the actual item via DI.</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="00a51-405">サービスへの静的なアクセスを回避します (たとえば、他所で使用するための [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) の静的な型指定)。</span><span class="sxs-lookup"><span data-stu-id="00a51-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span> <span data-ttu-id="00a51-406">*サービス ロケーター パターン*の使用は避けてください。</span><span class="sxs-lookup"><span data-stu-id="00a51-406">Avoid using the *service locator pattern*.</span></span>

* <span data-ttu-id="00a51-407">たとえば、サービス インスタンスを取得する場合、DI を使用できるときに、<xref:System.IServiceProvider.GetService*> を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

<span data-ttu-id="00a51-408">**正しくない:**</span><span class="sxs-lookup"><span data-stu-id="00a51-408">**Incorrect:**</span></span> <span data-ttu-id="00a51-409">**正しい**:</span><span class="sxs-lookup"><span data-stu-id="00a51-409">**Correct**:</span></span>

<span data-ttu-id="00a51-410">回避すべき別のサービス ロケーターのバリエーションは、実行時に依存関係を解決するファクトリを挿入することです。</span><span class="sxs-lookup"><span data-stu-id="00a51-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="00a51-411">この両方のプラクティスによって、複数の[制御の反転](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)方式が組み合わせられます。</span><span class="sxs-lookup"><span data-stu-id="00a51-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="00a51-412">`HttpContext` への静的なアクセスを回避します (たとえば [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="00a51-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="00a51-413">どのような推奨事項であっても、それを無視する必要がある状況が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="00a51-414">例外はまれです。ほとんどがフレームワーク自体の内の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="00a51-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="00a51-415">DI は静的/グローバル オブジェクト アクセス パターンの*代替機能*です。</span><span class="sxs-lookup"><span data-stu-id="00a51-415">DI is an *alternative* to static/global object access patterns.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00a51-416">静的オブジェクト アクセスと併用した場合、DI のメリットを実現することはできません。</span><span class="sxs-lookup"><span data-stu-id="00a51-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="00a51-417">DI でのマルチテナントの推奨パターン</span><span class="sxs-lookup"><span data-stu-id="00a51-417">Recommended patterns for multi-tenancy in DI</span></span>
* <span data-ttu-id="00a51-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) はマルチテナント機能があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span>
* <span data-ttu-id="00a51-419">詳細については、[Orchard Core のドキュメント](https://docs.orchardcore.net/en/dev/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>
* <span data-ttu-id="00a51-420">CMS 固有の機能を使用せずに Orchard Core Framework のみを使用してモジュラーおよびマルチテナント アプリを構築する方法の例については、 https://github.com/OrchardCMS/OrchardCore.Samples のサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>
* <span data-ttu-id="00a51-421">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="00a51-421">Additional resources</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[<span data-ttu-id="00a51-422">ASP.NET Core で IDisposables を破棄する 4 つの方法</span><span class="sxs-lookup"><span data-stu-id="00a51-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)

[<span data-ttu-id="00a51-423">依存関係の挿入による ASP.NET Core でのクリーンなコードの作成 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="00a51-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)

[<span data-ttu-id="00a51-424">明示的な依存関係の原則</span><span class="sxs-lookup"><span data-stu-id="00a51-424">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)

## <a name="overview-of-dependency-injection"></a>[<span data-ttu-id="00a51-425">制御の反転コンテナーと依存関係の挿入パターン (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="00a51-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)

[<span data-ttu-id="00a51-426">ASP.NET Core DI 内の複数のインターフェイスにサービスを登録する方法</span><span class="sxs-lookup"><span data-stu-id="00a51-426">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) <span data-ttu-id="00a51-427">ASP.NET Core では依存関係の挿入 (DI) ソフトウェア設計パターンがサポートされています。これは、クラスとその依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための手法です。</span><span class="sxs-lookup"><span data-stu-id="00a51-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="00a51-428">MVC コントローラー内部における依存関係の挿入に固有の情報については、<xref:mvc/controllers/dependency-injection> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="00a51-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="00a51-429">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="00a51-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="00a51-430">依存関係の挿入の概要</span><span class="sxs-lookup"><span data-stu-id="00a51-430">Overview of dependency injection</span></span> <span data-ttu-id="00a51-431">"*依存関係*" とは、他のオブジェクトが必要とする任意のオブジェクトのことです。</span><span class="sxs-lookup"><span data-stu-id="00a51-431">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="00a51-432">アプリ内の他のクラスが依存している、次の `WriteMessage` メソッドを備えた `MyDependency` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="00a51-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="00a51-433">クラスで `WriteMessage` メソッドを使用できるようにするために、`MyDependency` クラスのインスタンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="00a51-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="00a51-434">`MyDependency` クラスは `IndexModel` クラスの依存関係です。</span><span class="sxs-lookup"><span data-stu-id="00a51-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="00a51-435">このクラスは `MyDependency` のインスタンスを作成し、これに直接依存しています。</span><span class="sxs-lookup"><span data-stu-id="00a51-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="00a51-436">コードの依存関係 (前の例など) には問題が含まれ、次の理由から回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="00a51-437">`MyDependency` を別の実装で置き換えるには、クラスを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="00a51-438">`MyDependency` が依存関係を含んでいる場合、これらはクラスによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="00a51-439">複数のクラスが `MyDependency` に依存している大規模なプロジェクトでは、構成コードがアプリ全体に分散するようになります。</span><span class="sxs-lookup"><span data-stu-id="00a51-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="00a51-440">このような実装では、単体テストを行うことが困難です。</span><span class="sxs-lookup"><span data-stu-id="00a51-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="00a51-441">アプリはモックまたはスタブの `MyDependency` クラスを使用する必要がありますが、この方法では不可能です。</span><span class="sxs-lookup"><span data-stu-id="00a51-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="00a51-442">依存関係の挿入は、次の方法によってこれらの問題に対応します。</span><span class="sxs-lookup"><span data-stu-id="00a51-442">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="00a51-443">依存関係の実装を抽象化するための、インターフェイスまたは基底クラスの使用。</span><span class="sxs-lookup"><span data-stu-id="00a51-443">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="00a51-444">サービス コンテナー内の依存関係の登録。</span><span class="sxs-lookup"><span data-stu-id="00a51-444">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="00a51-445">ASP.NET Core には、組み込みのサービス コンテナー <xref:System.IServiceProvider> が用意されています。</span><span class="sxs-lookup"><span data-stu-id="00a51-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="00a51-446">サービスはアプリの `Startup.ConfigureServices` メソッドに登録されています。</span><span class="sxs-lookup"><span data-stu-id="00a51-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="00a51-447">サービスを使用するクラスのコンストラクターへの、サービスの "*挿入*"。</span><span class="sxs-lookup"><span data-stu-id="00a51-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="00a51-448">依存関係のインスタンスの作成、およびインスタンスが不要になったときの廃棄の役割を、フレームワークが担当します。</span><span class="sxs-lookup"><span data-stu-id="00a51-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="00a51-449">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)では、サービスがアプリに提供するメソッドが `IMyDependency` インターフェイスによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="00a51-450">このインターフェイスは、具象型 `MyDependency` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="00a51-451">`MyDependency` では、コンストラクター内で <xref:Microsoft.Extensions.Logging.ILogger`1> が要求されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="00a51-452">依存関係の挿入をチェーン形式で使用することはよくあります。</span><span class="sxs-lookup"><span data-stu-id="00a51-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="00a51-453">次に、要求されたそれぞれの依存関係が、それ自身の依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="00a51-453">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="00a51-454">コンテナーによってグラフ内の依存関係が解決され、完全に解決されたサービスが返されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="00a51-455">解決する必要がある依存関係の集合的なセットは、通常、"*依存関係ツリー*"、"*依存関係グラフ*"、または "*オブジェクト グラフ*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="00a51-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="00a51-456">`IMyDependency` と `ILogger<TCategoryName>` をサービス コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="00a51-457">`IMyDependency` は `Startup.ConfigureServices` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="00a51-458">`ILogger<TCategoryName>` はログ記録の抽象化インフラストラクチャによって登録されます。したがって、これは、フレームワークによって既定で登録される[フレームワークが提供するサービス](#framework-provided-services)です。</span><span class="sxs-lookup"><span data-stu-id="00a51-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="00a51-459">コンテナーでは、[(ジェネリック) オープン型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)を活用し、すべての [(ジェネリック) 構築型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)を登録する必要をなくすことで、`ILogger<TCategoryName>` を解決します。</span><span class="sxs-lookup"><span data-stu-id="00a51-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="00a51-460">サンプル アプリにおいて、`IMyDependency` サービスは `MyDependency` 具象型を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="00a51-461">登録によって、サービスの有効期間が 1 つの要求の有効期間に範囲設定されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="00a51-462">[サービスの有効期間](#service-lifetimes)については、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="00a51-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="00a51-463">各 `services.Add{SERVICE_NAME}` 拡張メソッドは、サービスを追加 (および場合によっては構成) します。</span><span class="sxs-lookup"><span data-stu-id="00a51-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="00a51-464">たとえば、`services.AddMvc()` はサービスの Razor Pages と必須の MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="00a51-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="00a51-465">アプリをこの規則に従わせることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="00a51-465">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="00a51-466">拡張メソッドを [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 名前空間に配置して、サービス登録のグループをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="00a51-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="00a51-467">サービスのコンストラクターで[ビルトイン型](/dotnet/csharp/language-reference/keywords/built-in-types-table) (`string` など) が必要な場合は、[構成](xref:fundamentals/configuration/index)や[オプション パターン](xref:fundamentals/configuration/options)を使って型を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="00a51-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="00a51-468">クラスのコンストラクター経由でサービスのインスタンスが要求されます。サービスはプライベート フィールドに割り当てられて使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="00a51-469">このフィールドは、クラス全体で必要に応じてサービスにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-469">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="00a51-470">サンプル アプリでは、`IMyDependency` インスタンスが要求され、サービスの `WriteMessage` メソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="00a51-471">Startup に挿入されるサービス</span><span class="sxs-lookup"><span data-stu-id="00a51-471">Services injected into Startup</span></span> <span data-ttu-id="00a51-472">汎用ホスト (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) を使用すると、次のサービスの種類のみを `Startup` コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="00a51-473">サービスは `Startup.Configure` に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-473">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="00a51-474">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-474">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="00a51-475">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="00a51-475">Framework-provided services</span></span> | <span data-ttu-id="00a51-476">`Startup.ConfigureServices` メソッドでは、Entity Framework Core や ASP.NET Core MVC のようなプラットフォーム機能など、アプリが使うサービスを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="00a51-477">最初に、`ConfigureServices` に提供される `IServiceCollection` には、フレームワークによって定義されたサービスがあります ([ホストの構成方法](xref:fundamentals/index#host)によって異なります)。</span><span class="sxs-lookup"><span data-stu-id="00a51-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="00a51-478">ASP.NET Core テンプレートに基づくアプリが、フレームワークによって登録された何百ものサービスを持つことも珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="00a51-479">次の表に、フレームワークによって登録されたサービスのごく一部のサンプルを示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-479">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="00a51-480">サービスの種類</span><span class="sxs-lookup"><span data-stu-id="00a51-480">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="00a51-481">有効期間</span><span class="sxs-lookup"><span data-stu-id="00a51-481">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="00a51-482">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="00a51-483">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-483">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="00a51-484">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="00a51-485">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="00a51-486">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="00a51-487">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="00a51-488">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-488">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="00a51-489">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="00a51-490">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="00a51-491">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-491">Singleton</span></span>

<span data-ttu-id="00a51-492">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-492">Transient</span></span> <span data-ttu-id="00a51-493">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-493">Singleton</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="00a51-494">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-494">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="00a51-495">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-495">Singleton</span></span>

<span data-ttu-id="00a51-496">拡張メソッドを使用した追加サービスの登録する</span><span class="sxs-lookup"><span data-stu-id="00a51-496">Register additional services with extension methods</span></span> <span data-ttu-id="00a51-497">サービス (および必要であればサービスが依存するサービス) を登録するためにサービス コレクションの拡張メソッドを使用できる場合は、1 つの `Add{SERVICE_NAME}` 拡張メソッドを使用してそのサービスが必要とするすべてのサービスを登録することが規則です。</span><span class="sxs-lookup"><span data-stu-id="00a51-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="00a51-498">次のコードは、拡張メソッド [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) と <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> を使用して、コンテナーに追加のサービスを追加する方法の例です。</span><span class="sxs-lookup"><span data-stu-id="00a51-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="00a51-499">詳細については、API ドキュメントにある <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> クラスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="00a51-500">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="00a51-500">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="00a51-501">登録される各サービスの適切な有効期間を選択します。</span><span class="sxs-lookup"><span data-stu-id="00a51-501">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="00a51-502">ASP.NET Core サービスは、次の有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="00a51-503">一時的</span><span class="sxs-lookup"><span data-stu-id="00a51-503">Transient</span></span> <span data-ttu-id="00a51-504">有効期間が一時的なサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) は、サービス コンテナーから要求されるたびに作成されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="00a51-505">この有効期間は、軽量でステートレスのサービスに最適です。</span><span class="sxs-lookup"><span data-stu-id="00a51-505">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="00a51-506">スコープ</span><span class="sxs-lookup"><span data-stu-id="00a51-506">Scoped</span></span>

<span data-ttu-id="00a51-507">有効期間がスコープのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) は、クライアント要求 (接続) ごとに 1 回作成されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-507">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="00a51-508">ミドルウェアでスコープ サービスを使用している場合、サービスを `Invoke` または `InvokeAsync` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="00a51-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="00a51-509">コンストラクターを使用して挿入すると、サービスがシングルトンのように動作するよう強制されるので、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用した挿入は行わないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="00a51-510">詳細については、「<xref:fundamentals/middleware/write#per-request-middleware-dependencies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a51-511">シングルトン</span><span class="sxs-lookup"><span data-stu-id="00a51-511">Singleton</span></span> <span data-ttu-id="00a51-512">有効期間がシングルトンのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) は、最初に要求されたときに作成されます (または、`Startup.ConfigureServices` が実行されて、サービス登録でインスタンスが指定された場合)。</span><span class="sxs-lookup"><span data-stu-id="00a51-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="00a51-513">以降の要求は、すべて同じインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="00a51-513">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="00a51-514">アプリをシングルトンで動作させる必要がある場合は、サービス コンテナーによるサービスの有効期間の管理を許可することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="00a51-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="00a51-515">クラス内のオブジェクトの有効期間を管理するために、シングルトンの設計パターンを実装してユーザー コードを提供しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="00a51-516">シングルトンからスコープ サービスを解決するのは危険です。</span><span class="sxs-lookup"><span data-stu-id="00a51-516">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="00a51-517">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-517">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="00a51-518">サービス登録メソッド</span><span class="sxs-lookup"><span data-stu-id="00a51-518">Service registration methods</span></span> | <span data-ttu-id="00a51-519">サービス登録拡張メソッドでは、特定のシナリオで役立つオーバーロードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-519">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="00a51-520">メソッド</span><span class="sxs-lookup"><span data-stu-id="00a51-520">Method</span></span> | <span data-ttu-id="00a51-521">自動</span><span class="sxs-lookup"><span data-stu-id="00a51-521">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="00a51-522">object</span><span class="sxs-lookup"><span data-stu-id="00a51-522">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="00a51-523">破棄</span><span class="sxs-lookup"><span data-stu-id="00a51-523">disposal</span></span> | <span data-ttu-id="00a51-524">複数</span><span class="sxs-lookup"><span data-stu-id="00a51-524">Multiple</span></span> | <span data-ttu-id="00a51-525">実装</span><span class="sxs-lookup"><span data-stu-id="00a51-525">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="00a51-526">引数を渡す</span><span class="sxs-lookup"><span data-stu-id="00a51-526">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="00a51-527">例:</span><span class="sxs-lookup"><span data-stu-id="00a51-527">Example:</span></span> | <span data-ttu-id="00a51-528">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-528">Yes</span></span> | <span data-ttu-id="00a51-529">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="00a51-530">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-530">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="00a51-531">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-531">Examples:</span></span> | <span data-ttu-id="00a51-532">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-532">Yes</span></span> | <span data-ttu-id="00a51-533">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-533">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="00a51-534">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-534">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="00a51-535">例:</span><span class="sxs-lookup"><span data-stu-id="00a51-535">Example:</span></span> | <span data-ttu-id="00a51-536">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-536">Yes</span></span> | <span data-ttu-id="00a51-537">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-537">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="00a51-538">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-538">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="00a51-539">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-539">Examples:</span></span> | <span data-ttu-id="00a51-540">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-540">No</span></span> | <span data-ttu-id="00a51-541">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-541">Yes</span></span> |

<span data-ttu-id="00a51-542">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-542">Yes</span></span> <span data-ttu-id="00a51-543">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-543">Examples:</span></span>

<span data-ttu-id="00a51-544">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-544">No</span></span>

<span data-ttu-id="00a51-545">いいえ</span><span class="sxs-lookup"><span data-stu-id="00a51-545">No</span></span> <span data-ttu-id="00a51-546">はい</span><span class="sxs-lookup"><span data-stu-id="00a51-546">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="00a51-547">型の廃棄の詳細については、「[サービスの破棄](#disposal-of-services)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-547">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="00a51-548">実装が複数の場合の一般的なシナリオとしては、[テスト用に型のモックを作成](xref:test/integration-tests#inject-mock-services)します。</span><span class="sxs-lookup"><span data-stu-id="00a51-548">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="00a51-549">`TryAdd{LIFETIME}` メソッドでは、登録された実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-549">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="00a51-550">次の例の最初の行では、`IMyDependency` に `MyDependency` を登録します。</span><span class="sxs-lookup"><span data-stu-id="00a51-550">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="00a51-551">2 行目では何も行われません。`IMyDependency` には登録された実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="00a51-551">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="00a51-552">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="00a51-552">For more information, see:</span></span> <span data-ttu-id="00a51-553">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) メソッドでは、"*同じ型*" の実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-553">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="00a51-554">複数のサービスは、`IEnumerable<{SERVICE}>` によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-554">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="00a51-555">サービスを登録するとき、開発者は同じ型のものがまだ追加されていない場合にのみインスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="00a51-555">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="00a51-556">一般に、このメソッドは、インスタンスのコピーが 2 つコンテナーに登録されるのを回避するために、ライブラリ作成者によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-556">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="00a51-557">次の例の最初の行では、`IMyDep1` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="00a51-557">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="00a51-558">2 行目では `IMyDep2` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="00a51-558">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="00a51-559">3 行目では何も行われません。`IMyDep1` には `MyDep` の登録済みの実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="00a51-559">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="00a51-560">コンストラクターの挿入の動作</span><span class="sxs-lookup"><span data-stu-id="00a51-560">Constructor injection behavior</span></span>

<span data-ttu-id="00a51-561">サービスは 2 つのメカニズムによって解決できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-561">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="00a51-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:依存関係の挿入コンテナーにサービスを登録することなくオブジェクトの作成を許可します。</span><span class="sxs-lookup"><span data-stu-id="00a51-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="00a51-563">`ActivatorUtilities` はユーザー側の抽象化 (タグ ヘルパー、MVC コントローラー、モデル バインダーなど) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-563">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="00a51-564">コンストラクターは、依存関係の挿入によって提供されない引数を受け取ることができますが、引数は既定値を割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-564">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="00a51-565">`IServiceProvider` または `ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、"*パブリック*" コンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="00a51-565">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="00a51-566">`ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、該当するコンストラクターが 1 つだけ存在することが必要です。</span><span class="sxs-lookup"><span data-stu-id="00a51-566">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="00a51-567">コンストラクターのオーバーロードはサポートされていますが、依存関係の挿入によってすべての引数を設定できるオーバーロードは 1 つしか存在できません。</span><span class="sxs-lookup"><span data-stu-id="00a51-567">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="00a51-568">Entity Framework コンテキスト</span><span class="sxs-lookup"><span data-stu-id="00a51-568">Entity Framework contexts</span></span> <span data-ttu-id="00a51-569">Entity Framework コンテキストでは通常、[範囲が指定された有効期間](#service-lifetimes)が利用され、サービス コンテナーに追加されます。これは、Web アプリ データベース操作は通常、その範囲がクライアント要求に設定されるためです。</span><span class="sxs-lookup"><span data-stu-id="00a51-569">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="00a51-570">データベース コンテキストの登録時、[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) オーバーロードによって有効期間が指定されなかった場合、既定の有効期間が範囲となります。</span><span class="sxs-lookup"><span data-stu-id="00a51-570">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="00a51-571">有効期間が与えられたサービスの場合、サービスより有効期間が短いデータベース コンテキストを使用できません。</span><span class="sxs-lookup"><span data-stu-id="00a51-571">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="00a51-572">有効期間と登録のオプション</span><span class="sxs-lookup"><span data-stu-id="00a51-572">Lifetime and registration options</span></span> <span data-ttu-id="00a51-573">有効期間と登録のオプションの違いを示すために、タスクを一意の識別子 `OperationId` を備えた操作として表す、次のインターフェイスについて考えます。</span><span class="sxs-lookup"><span data-stu-id="00a51-573">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="00a51-574">次のインターフェイスに対して操作のサービスの有効期間がどのように構成されているかに応じて、コンテナーは、クラスに要求されたときに、サービスの同じインスタンスか別のインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="00a51-574">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="00a51-575">インターフェイスは `Operation` クラス内で実装されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-575">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="00a51-576">指定されない場合、`Operation` コンストラクターは GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-576">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="00a51-577">`OperationService` が登録されます。これは、その他の `Operation` 型のそれぞれに依存します。</span><span class="sxs-lookup"><span data-stu-id="00a51-577">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="00a51-578">依存関係の挿入を経由して `OperationService` が要求される場合、これは各サービスの新しいインスタンスか、依存関係のあるサービスの有効期間に基づく既存のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="00a51-578">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="00a51-579">コンテナーからの要求時に一時的なサービスが作成されるとき、`IOperationTransient` サービスの `OperationId` と `OperationService` の `OperationId` は異なります。</span><span class="sxs-lookup"><span data-stu-id="00a51-579">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="00a51-580">`OperationService` は `IOperationTransient` クラスの新しいインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="00a51-580">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="00a51-581">新しいインスタンスは異なる `OperationId` を生成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-581">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="00a51-582">クライアント要求ごとにスコープ サービスが作成されるとき、`IOperationScoped` サービスの `OperationId` は、クライアント要求内の `OperationService` のものと同じになります。</span><span class="sxs-lookup"><span data-stu-id="00a51-582">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="00a51-583">クライアント要求間で、両方のサービスは異なる `OperationId` 値を共有します。</span><span class="sxs-lookup"><span data-stu-id="00a51-583">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="00a51-584">シングルトンとシングルトン インスタンスのサービスが 1 回作成され、すべてのクライアント要求とすべてのサービス間で使用されるとき、`OperationId` はすべてのサービス要求の間で一定です。</span><span class="sxs-lookup"><span data-stu-id="00a51-584">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="00a51-585">`Startup.ConfigureServices` では、各型が名前で指定されている有効期間に従ってコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-585">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="00a51-586">`IOperationSingletonInstance` サービスは、`Guid.Empty` の既知の ID を持った特定のインスタンスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="00a51-586">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="00a51-587">この型が使用されるタイミングは明らかです (その GUID はすべてゼロです)。</span><span class="sxs-lookup"><span data-stu-id="00a51-587">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="00a51-588">サンプル アプリでは、個々の要求内、および個々の要求間におけるオブジェクトの有効期間が示されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-588">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="00a51-589">サンプル アプリの `IndexModel` には、各種類の `IOperation` 型と `OperationService` が必要です。</span><span class="sxs-lookup"><span data-stu-id="00a51-589">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="00a51-590">次に、ページ モデル クラスとサービスのすべての `OperationId` 値が、プロパティの割り当てを通じてページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-590">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="00a51-591">2 つの要求の結果を、以下の 2 つの出力に示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-591">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="00a51-592">**最初の要求:**</span><span class="sxs-lookup"><span data-stu-id="00a51-592">**First request:**</span></span>

<span data-ttu-id="00a51-593">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="00a51-593">Controller operations:</span></span>

<span data-ttu-id="00a51-594">一時的: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="00a51-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="00a51-595">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="00a51-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="00a51-596">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="00a51-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="00a51-597">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="00a51-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="00a51-598">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="00a51-598">`OperationService` operations:</span></span>

<span data-ttu-id="00a51-599">一時的: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="00a51-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="00a51-600">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="00a51-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="00a51-601">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="00a51-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="00a51-602">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="00a51-602">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="00a51-603">**2 番目の要求:**</span><span class="sxs-lookup"><span data-stu-id="00a51-603">**Second request:**</span></span>

<span data-ttu-id="00a51-604">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="00a51-604">Controller operations:</span></span>

<span data-ttu-id="00a51-605">一時的: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="00a51-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="00a51-606">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="00a51-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="00a51-607">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="00a51-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="00a51-608">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="00a51-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="00a51-609">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="00a51-609">`OperationService` operations:</span></span>

* <span data-ttu-id="00a51-610">一時的: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="00a51-610">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="00a51-611">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="00a51-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="00a51-612">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="00a51-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="00a51-613">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="00a51-613">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="00a51-614">要求内および要求間で、どの `OperationId` 値が変化しているかを確認してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-614">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="00a51-615">"*一時的な*" オブジェクトは常に異なります。</span><span class="sxs-lookup"><span data-stu-id="00a51-615">*Transient* objects are always different.</span></span>

<span data-ttu-id="00a51-616">1 番目と 2 番目のクライアント要求の一時的な `OperationId` 値は、`OperationService` 操作とクライアント要求間の両方に対して異なります。</span><span class="sxs-lookup"><span data-stu-id="00a51-616">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="00a51-617">個々のサービス要求とクライアント要求に対して、新しいインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-617">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="00a51-618">*Scoped* オブジェクトは、1 つのクライアント要求内では同じですが、複数のクライアント要求間では異なります。</span><span class="sxs-lookup"><span data-stu-id="00a51-618">*Scoped* objects are the same within a client request but different across client requests.</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="00a51-619">"*シングルトン*" オブジェクトは、`Operation` インスタンスが `Startup.ConfigureServices` で提供されるかどうかに関係なく、すべてのオブジェクトとすべての要求について同じです。</span><span class="sxs-lookup"><span data-stu-id="00a51-619">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="00a51-620">main からサービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="00a51-620">Call services from main</span></span>

* <span data-ttu-id="00a51-621">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) を使用して <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成し、アプリのスコープ内のスコープ サービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="00a51-621">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="00a51-622">この方法は、起動時に初期化タスクを実行するために、スコープ サービスにアクセスするのに便利です。</span><span class="sxs-lookup"><span data-stu-id="00a51-622">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="00a51-623">次の例では、`Program.Main` で `MyScopedService` のコンテキストを取得する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-623">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="00a51-624">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="00a51-624">Scope validation</span></span>

<span data-ttu-id="00a51-625">アプリが開発環境で実行されている場合、既定のサービス プロバイダーは次を確認するためのチェックを実行します。</span><span class="sxs-lookup"><span data-stu-id="00a51-625">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="00a51-626">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="00a51-626">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="00a51-627">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="00a51-627">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="00a51-628"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-628">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="00a51-629">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-629">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="00a51-630">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-630">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="00a51-631">ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-631">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="00a51-632">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="00a51-632">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="00a51-633">詳細については、「<xref:fundamentals/host/web-host#scope-validation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00a51-633">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="00a51-634">要求サービス</span><span class="sxs-lookup"><span data-stu-id="00a51-634">Request Services</span></span> <span data-ttu-id="00a51-635">`HttpContext` からの ASP.NET Core 要求内で使用可能なサービスは、[HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) コレクションを通じて公開されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-635">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="00a51-636">要求サービスは、アプリの一部として構成および要求されるサービスを表します。</span><span class="sxs-lookup"><span data-stu-id="00a51-636">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="00a51-637">オブジェクトで依存関係を指定すると、これらは `ApplicationServices` ではなく `RequestServices` で検出された型で満たされます。</span><span class="sxs-lookup"><span data-stu-id="00a51-637">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="00a51-638">一般に、アプリから直接これらのプロパティを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-638">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="00a51-639">代わりに、クラスのコンストラクターを介してクラスに必要な型を要求し、フレームワークに依存関係を挿入させます。</span><span class="sxs-lookup"><span data-stu-id="00a51-639">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span>
* <span data-ttu-id="00a51-640">これにより、テストしやすいクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-640">This yields classes that are easier to test.</span></span> <span data-ttu-id="00a51-641">コンストラクターのパラメーターとして依存関係を要求し、`RequestServices` コレクションにアクセスするようにします。</span><span class="sxs-lookup"><span data-stu-id="00a51-641">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="00a51-642">依存関係の挿入のためのサービスの設計</span><span class="sxs-lookup"><span data-stu-id="00a51-642">Design services for dependency injection</span></span> <span data-ttu-id="00a51-643">ベスト プラクティスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="00a51-643">Best practices are to:</span></span>
* <span data-ttu-id="00a51-644">各依存関係を取得するために依存関係の挿入を使用するようサービスを設計します。</span><span class="sxs-lookup"><span data-stu-id="00a51-644">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="00a51-645">ステートフル、静的クラス、およびメンバーは避けてください。</span><span class="sxs-lookup"><span data-stu-id="00a51-645">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="00a51-646">代わりにシングルトン サービスを使用するようにアプリを設計し、グローバルな状態を作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="00a51-646">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="00a51-647">サービス内部で依存関係のあるクラスを直接インスタンス化することを回避します。</span><span class="sxs-lookup"><span data-stu-id="00a51-647">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="00a51-648">直接のインスタンス化は、コードの固有の実装につながります。</span><span class="sxs-lookup"><span data-stu-id="00a51-648">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="00a51-649">アプリのクラスを、小さく、十分に要素に分割された、テストしやすいものにします。</span><span class="sxs-lookup"><span data-stu-id="00a51-649">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="00a51-650">クラスに含まれる挿入される依存関係が多すぎるように見える場合、これは通常、クラスが担当する役割が多すぎて、[単一責任の原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) に違反していることのサインです。</span><span class="sxs-lookup"><span data-stu-id="00a51-650">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="00a51-651">責任の一部を新しいクラスに移動することにより、クラスのリファクタリングを試みます。</span><span class="sxs-lookup"><span data-stu-id="00a51-651">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="00a51-652">Razor Pages のページ モデル クラスと MVC コントローラー クラスは、UI の問題に集中する必要があることに留意します。</span><span class="sxs-lookup"><span data-stu-id="00a51-652">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="00a51-653">ビジネス ルールとデータ アクセスの実装の詳細は、これらの[個別の問題](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)に適したクラスに分離する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-653">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="00a51-654">サービスの破棄</span><span class="sxs-lookup"><span data-stu-id="00a51-654">Disposal of services</span></span>
* <span data-ttu-id="00a51-655">コンテナーは、作成する <xref:System.IDisposable> 型の <xref:System.IDisposable.Dispose*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="00a51-655">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="00a51-656">ユーザー コードによってコンテナーに追加されたインスタンスは、自動的に破棄されません。</span><span class="sxs-lookup"><span data-stu-id="00a51-656">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="00a51-657">次の例では、サービスがサービス コンテナーによって作成され、自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-657">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="00a51-658">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="00a51-658">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="00a51-659">サービス インスタンスは、サービス コンテナーによって作成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-659">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="00a51-660">サービスの有効期間は、フレームワークによって認識されません。</span><span class="sxs-lookup"><span data-stu-id="00a51-660">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="00a51-661">フレームワークでは、サービスが自動的に破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-661">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="00a51-662">サービスが開発者コードで明示的に破棄されていない場合は、アプリがシャットダウンするまで保持されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-662">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="00a51-663">一時的なインスタンスと共有インスタンスのための IDisposable ガイダンス</span><span class="sxs-lookup"><span data-stu-id="00a51-663">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="00a51-664">一時的で限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="00a51-664">Transient, limited lifetime</span></span>

<span data-ttu-id="00a51-665">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="00a51-665">**Scenario**</span></span> <span data-ttu-id="00a51-666">アプリには、次のいずれかのシナリオに対して一時的な有効期間を持つ <xref:System.IDisposable> インスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="00a51-666">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="00a51-667">インスタンスは、ルート スコープで解決されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-667">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="00a51-668">インスタンスは、スコープが終了する前に破棄する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-668">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="00a51-669">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="00a51-669">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="00a51-670">ファクトリ パターンを使用して、親スコープの外部にインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-670">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="00a51-671">このような状況では、通常、アプリケーションには、最終的な型のコンストラクターを直接呼び出す `Create` メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="00a51-671">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="00a51-672">最終的な型に他の依存関係がある場合、ファクトリは次のことができます。</span><span class="sxs-lookup"><span data-stu-id="00a51-672">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="00a51-673">そのコンストラクターで <xref:System.IServiceProvider> を受け取る。</span><span class="sxs-lookup"><span data-stu-id="00a51-673">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="00a51-674"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> を使用してコンテナー外部でインスタンスをインスタンス化し、同時にコンテナーを依存関係のために使用する。</span><span class="sxs-lookup"><span data-stu-id="00a51-674">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="00a51-675">共有インスタンス、限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="00a51-675">Shared Instance, limited lifetime</span></span> <span data-ttu-id="00a51-676">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="00a51-676">**Scenario**</span></span> <span data-ttu-id="00a51-677">アプリでは、複数のサービスにわたって共有 <xref:System.IDisposable> インスタンスが必要ですが、<xref:System.IDisposable> の有効期間は限られています。</span><span class="sxs-lookup"><span data-stu-id="00a51-677">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="00a51-678">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="00a51-678">**Solution**</span></span>

* <span data-ttu-id="00a51-679">インスタンスをスコープ付きの有効期間に登録します。</span><span class="sxs-lookup"><span data-stu-id="00a51-679">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="00a51-680"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> を使用してを開始し、新しい <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-680">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="00a51-681">スコープの <xref:System.IServiceProvider> を使用して必要なサービスを取得します。</span><span class="sxs-lookup"><span data-stu-id="00a51-681">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="00a51-682">有効期間が終了するときにスコープを破棄します。</span><span class="sxs-lookup"><span data-stu-id="00a51-682">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="00a51-683">一般的なガイドライン</span><span class="sxs-lookup"><span data-stu-id="00a51-683">General Guidelines</span></span> <span data-ttu-id="00a51-684"><xref:System.IDisposable> インスタンスは、一時的なスコープを使用して登録しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-684">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="00a51-685">代わりに、ファクトリ パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="00a51-685">Use the factory pattern instead.</span></span> <span data-ttu-id="00a51-686">一時的またはスコープ付きの <xref:System.IDisposable> インスタンスをルート スコープ内で解決しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-686">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="00a51-687">唯一の一般的な例外は、アプリが <xref:System.IServiceProvider> を作成/再作成し、破棄する場合です。これは理想的なパターンではありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-687">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="00a51-688">DI を使用して <xref:System.IDisposable> 依存関係を受け取る場合、受信側が <xref:System.IDisposable> 自体を実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-688">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="00a51-689"><xref:System.IDisposable> 依存関係の受信側は、その依存関係で <xref:System.IDisposable.Dispose%2A> を呼び出してはなりません。</span><span class="sxs-lookup"><span data-stu-id="00a51-689">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="00a51-690">サービスの有効期間を制御するには、スコープを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-690">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="00a51-691">スコープは階層構造ではなく、スコープ間に特別な接続はありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-691">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="00a51-692">既定のサービス コンテナーの置換</span><span class="sxs-lookup"><span data-stu-id="00a51-692">Default service container replacement</span></span>
* <span data-ttu-id="00a51-693">組み込みのサービス コンテナーは、フレームワークと、ほとんどのコンシューマー アプリのニーズに対応することを目的としたものです。</span><span class="sxs-lookup"><span data-stu-id="00a51-693">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="00a51-694">組み込みコンテナーでサポートされない、以下のような特定の機能が必要な場合を除き、組み込みコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="00a51-694">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="00a51-695">プロパティの挿入</span><span class="sxs-lookup"><span data-stu-id="00a51-695">Property injection</span></span>

<span data-ttu-id="00a51-696">名前に基づく挿入</span><span class="sxs-lookup"><span data-stu-id="00a51-696">Injection based on name</span></span>

* <span data-ttu-id="00a51-697">子コンテナー</span><span class="sxs-lookup"><span data-stu-id="00a51-697">Child containers</span></span>
* <span data-ttu-id="00a51-698">有効期間のカスタム管理</span><span class="sxs-lookup"><span data-stu-id="00a51-698">Custom lifetime management</span></span>
* <span data-ttu-id="00a51-699">遅延初期化に対する `Func<T>` のサポート</span><span class="sxs-lookup"><span data-stu-id="00a51-699">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="00a51-700">規則に基づく登録</span><span class="sxs-lookup"><span data-stu-id="00a51-700">Convention-based registration</span></span>
* <span data-ttu-id="00a51-701">次のサードパーティ コンテナーは ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="00a51-701">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* [<span data-ttu-id="00a51-702">Autofac</span><span class="sxs-lookup"><span data-stu-id="00a51-702">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="00a51-703">DryIoc</span><span class="sxs-lookup"><span data-stu-id="00a51-703">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>[<span data-ttu-id="00a51-704">Grace</span><span class="sxs-lookup"><span data-stu-id="00a51-704">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)

[<span data-ttu-id="00a51-705">LightInject</span><span class="sxs-lookup"><span data-stu-id="00a51-705">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection) [<span data-ttu-id="00a51-706">Lamar</span><span class="sxs-lookup"><span data-stu-id="00a51-706">Lamar</span></span>](https://jasperfx.github.io/lamar/)

[<span data-ttu-id="00a51-707">Stashbox</span><span class="sxs-lookup"><span data-stu-id="00a51-707">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection) [<span data-ttu-id="00a51-708">Unity</span><span class="sxs-lookup"><span data-stu-id="00a51-708">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="00a51-709">スレッド セーフ</span><span class="sxs-lookup"><span data-stu-id="00a51-709">Thread safety</span></span>

* <span data-ttu-id="00a51-710">スレッド セーフのシングルトン サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="00a51-710">Create thread-safe singleton services.</span></span> <span data-ttu-id="00a51-711">シングルトン サービスに一時的サービスへの依存関係がある場合、シングルトンによる一時的サービスの使い方によっては、一時的サービスもスレッド セーフであることが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-711">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="00a51-712">1 つのサービスのファクトリ メソッド (例: [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) に対する 2 番目の引数) をスレッド セーフにする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="00a51-712">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="00a51-713">型 (`static`) のコンストラクターのように、1 つのスレッドによって 1 回呼び出されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="00a51-713">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="00a51-714">推奨事項</span><span class="sxs-lookup"><span data-stu-id="00a51-714">Recommendations</span></span> <span data-ttu-id="00a51-715">`async/await` および `Task` ベースのサービスの解決はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="00a51-715">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="00a51-716">C# では非同期コンストラクターがサポートされていないため、推奨パターンは、サービスを同期的に解決した後に、非同期メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="00a51-716">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="00a51-717">データと構成をサービス コンテナーに直接格納しないようにします。</span><span class="sxs-lookup"><span data-stu-id="00a51-717">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="00a51-718">たとえば、通常、ユーザーのショッピング カートはサービス コンテナーに追加しません。</span><span class="sxs-lookup"><span data-stu-id="00a51-718">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span>

  * <span data-ttu-id="00a51-719">構成では、[オプション パターン](xref:fundamentals/configuration/options)を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-719">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

    <span data-ttu-id="00a51-720">同様に、他のオブジェクトへのアクセスを許可するためだけに存在する "データ ホルダー" オブジェクトは避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-720">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="00a51-721">実際のアイテムを DI 経由で要求することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="00a51-721">It's better to request the actual item via DI.</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * <span data-ttu-id="00a51-722">サービスへの静的なアクセスを回避します (たとえば、他所で使用するための [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) の静的な型指定)。</span><span class="sxs-lookup"><span data-stu-id="00a51-722">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="00a51-723">*サービス ロケーター パターン*は、[制御の反転](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)戦略が混在しているので使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-723">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

<span data-ttu-id="00a51-724">サービス インスタンスを取得する場合、DI を使用できるときに、<xref:System.IServiceProvider.GetService*> を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-724">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span> <span data-ttu-id="00a51-725">**正しくない:**</span><span class="sxs-lookup"><span data-stu-id="00a51-725">**Incorrect:**</span></span>

<span data-ttu-id="00a51-726">**正しい**:</span><span class="sxs-lookup"><span data-stu-id="00a51-726">**Correct**:</span></span> <span data-ttu-id="00a51-727"><xref:System.IServiceProvider.GetService*> を使用して、実行時に依存関係を解決するファクトリを挿入しないでください。</span><span class="sxs-lookup"><span data-stu-id="00a51-727">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00a51-728">`HttpContext` への静的なアクセスを回避します (たとえば [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="00a51-728">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="00a51-729">どのような推奨事項であっても、それを無視する必要がある状況が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="00a51-729">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span>
* <span data-ttu-id="00a51-730">例外はまれです。ほとんどがフレームワーク自体の内の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="00a51-730">Exceptions are rare, mostly special cases within the framework itself.</span></span>
* <span data-ttu-id="00a51-731">DI は静的/グローバル オブジェクト アクセス パターンの*代替機能*です。</span><span class="sxs-lookup"><span data-stu-id="00a51-731">DI is an *alternative* to static/global object access patterns.</span></span>
* <span data-ttu-id="00a51-732">静的オブジェクト アクセスと併用した場合、DI のメリットを実現することはできません。</span><span class="sxs-lookup"><span data-stu-id="00a51-732">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>
* <span data-ttu-id="00a51-733">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="00a51-733">Additional resources</span></span>

::: moniker-end
